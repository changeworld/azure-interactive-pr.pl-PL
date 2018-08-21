---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079469"
---
<span data-ttu-id="1e349-103">Aplikacja, którą tworzysz, to galeria zdjęć.</span><span class="sxs-lookup"><span data-stu-id="1e349-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="1e349-104">Używa ona języka JavaScript po stronie klienta do wywoływania interfejsów API umożliwiających przekazywanie i wyświetlanie obrazów.</span><span class="sxs-lookup"><span data-stu-id="1e349-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="1e349-105">W tym module utworzysz interfejs API, który przy użyciu funkcji bezserwerowej generuje ograniczony czasowo adres URL umożliwiający przekazanie obrazu.</span><span class="sxs-lookup"><span data-stu-id="1e349-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="1e349-106">Aplikacja internetowa używa wygenerowanego adresu URL do przekazania obrazu do usługi Blob Storage przy użyciu [interfejsu API REST usługi Blob Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="1e349-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="1e349-107">Tworzenie kontenera usługi Blob Storage do przechowywania obrazów</span><span class="sxs-lookup"><span data-stu-id="1e349-107">Create a blob storage container for images</span></span>

<span data-ttu-id="1e349-108">Aplikacja wymaga oddzielnego kontenera magazynu na potrzeby przekazywania i hostowania obrazów.</span><span class="sxs-lookup"><span data-stu-id="1e349-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="1e349-109">Upewnij się, że jesteś zalogowanym użytkownikiem usługi Cloud Shell (w powłoce bash).</span><span class="sxs-lookup"><span data-stu-id="1e349-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="1e349-110">Jeśli nie, wybierz pozycję **Wejdź w tryb koncentracji uwagi**, aby otworzyć okno usługi Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="1e349-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="1e349-111">Korzystając ze swojego konta magazynu, utwórz nowy kontener o nazwie **images** z publicznym dostępem do wszystkich obiektów blob.</span><span class="sxs-lookup"><span data-stu-id="1e349-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="1e349-112">Tworzenie aplikacji funkcji platformy Azure</span><span class="sxs-lookup"><span data-stu-id="1e349-112">Create an Azure Function app</span></span>

<span data-ttu-id="1e349-113">Azure Functions to usługa umożliwiająca uruchamianie funkcji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="1e349-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="1e349-114">Funkcja bezserwerowa może być wyzwalana (wywoływana) przez zdarzenia, takie jak żądanie HTTP lub utworzenie obiektu blob w kontenerze magazynu.</span><span class="sxs-lookup"><span data-stu-id="1e349-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="1e349-115">Aplikacja funkcji platformy Azure to kontener przeznaczony na jedną lub większą liczbę funkcji bezserwerowych.</span><span class="sxs-lookup"><span data-stu-id="1e349-115">An Azure Function app is a container for one or more serverless functions.</span></span>

1. <span data-ttu-id="1e349-116">Utwórz nową aplikację funkcji platformy Azure o unikatowej nazwie w utworzonej wcześniej grupie zasobów o nazwie **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="1e349-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="1e349-117">Aplikacje funkcji wymagają konta magazynu. W tym samouczku użyjesz istniejącego konta magazynu.</span><span class="sxs-lookup"><span data-stu-id="1e349-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="1e349-118">Tworzenie funkcji bezserwerowej wyzwalanej przez protokół HTTP</span><span class="sxs-lookup"><span data-stu-id="1e349-118">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="1e349-119">Aplikacja internetowa do obsługi galerii zdjęć wysyła żądanie HTTP do funkcji bezserwerowej w celu wygenerowania ograniczonego czasowo adresu URL na potrzeby bezpiecznego przekazania obrazu do usługi Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="1e349-119">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="1e349-120">Funkcja jest wyzwalana przez żądanie HTTP i używa zestawu SDK usługi Azure Storage w celu wygenerowania i zwrócenia bezpiecznego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="1e349-120">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="1e349-121">Po utworzeniu aplikacji funkcji wyszukaj ją w witrynie Azure Portal, korzystając z pola wyszukiwania, i kliknij jej nazwę, aby ją otworzyć.</span><span class="sxs-lookup"><span data-stu-id="1e349-121">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Otwieranie aplikacji funkcji](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="1e349-123">W obszarze nawigacji po lewej stronie okna aplikacji funkcji umieść kursor nad opcją **Funkcje** i kliknij znak **+**, aby rozpocząć tworzenie nowej funkcji bezserwerowej.</span><span class="sxs-lookup"><span data-stu-id="1e349-123">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Tworzenie nowej funkcji](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="1e349-125">Kliknij pozycję **Funkcja niestandardowa**, aby zobaczyć listę szablonów funkcji.</span><span class="sxs-lookup"><span data-stu-id="1e349-125">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="1e349-126">Znajdź szablon **HttpTrigger** i kliknij odpowiedni język (C# lub JavaScript).</span><span class="sxs-lookup"><span data-stu-id="1e349-126">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="1e349-127">Użyj następujących wartości, aby utworzyć funkcję, która generuje adres URL umożliwiający przekazanie obiektu blob.</span><span class="sxs-lookup"><span data-stu-id="1e349-127">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="1e349-128">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="1e349-128">Setting</span></span>      |  <span data-ttu-id="1e349-129">Sugerowana wartość</span><span class="sxs-lookup"><span data-stu-id="1e349-129">Suggested value</span></span>   | <span data-ttu-id="1e349-130">Opis</span><span class="sxs-lookup"><span data-stu-id="1e349-130">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="1e349-131">**Język**</span><span class="sxs-lookup"><span data-stu-id="1e349-131">**Language**</span></span> | <span data-ttu-id="1e349-132">C# lub JavaScript</span><span class="sxs-lookup"><span data-stu-id="1e349-132">C# or JavaScript</span></span> | <span data-ttu-id="1e349-133">Wybierz język, którego chcesz użyć.</span><span class="sxs-lookup"><span data-stu-id="1e349-133">Select the language you want to use.</span></span> |
    | <span data-ttu-id="1e349-134">**Nazwa funkcji**</span><span class="sxs-lookup"><span data-stu-id="1e349-134">**Name your function**</span></span> | <span data-ttu-id="1e349-135">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="1e349-135">GetUploadUrl</span></span> | <span data-ttu-id="1e349-136">Wpisz tę nazwę dokładnie tak, jak pokazano, aby aplikacja mogła odnaleźć funkcję.</span><span class="sxs-lookup"><span data-stu-id="1e349-136">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="1e349-137">**Poziom autoryzacji**</span><span class="sxs-lookup"><span data-stu-id="1e349-137">**Authorization level**</span></span> | <span data-ttu-id="1e349-138">Anonimowe</span><span class="sxs-lookup"><span data-stu-id="1e349-138">Anonymous</span></span> | <span data-ttu-id="1e349-139">Zezwalaj na publiczny dostęp do funkcji.</span><span class="sxs-lookup"><span data-stu-id="1e349-139">Allow the function to be accessed publicly.</span></span> |

    ![Wprowadzanie ustawień nowej funkcji wyzwalanej przez protokół HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="1e349-141">Kliknij przycisk **Utwórz**, aby utworzyć funkcję.</span><span class="sxs-lookup"><span data-stu-id="1e349-141">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="1e349-142">**C#**</span><span class="sxs-lookup"><span data-stu-id="1e349-142">**C#**</span></span> 

    1. <span data-ttu-id="1e349-143">Gdy pojawi się kod źródłowy funkcji, zastąp całą zawartość pliku **run.csx** zawartością pliku [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="1e349-143">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="1e349-144">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="1e349-144">**JavaScript**</span></span> 

    1. <span data-ttu-id="1e349-145">(JavaScript) Ta funkcja wymaga pakietu `azure-storage` narzędzia npm w celu wygenerowania tokenu sygnatury dostępu współdzielonego (SAS) wymaganego do utworzenia bezpiecznego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="1e349-145">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="1e349-146">Aby zainstalować pakiet npm, kliknij nazwę aplikacji funkcji w lewym obszarze nawigacji i kliknij pozycję **Funkcje platformy**.</span><span class="sxs-lookup"><span data-stu-id="1e349-146">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="1e349-147">(JavaScript) Kliknij pozycję **Konsola**, aby wyświetlić okno konsoli.</span><span class="sxs-lookup"><span data-stu-id="1e349-147">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Otwieranie okna konsoli](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="1e349-149">(JavaScript) Upewnij się, bieżący katalog to **d:\home\site\wwwroot**, uruchamiając polecenie `cd d:\home\site\wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="1e349-149">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="1e349-150">(JavaScript) Uruchom polecenie `npm init -y`, aby utworzyć pusty plik **package.json**.</span><span class="sxs-lookup"><span data-stu-id="1e349-150">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="1e349-151">(JavaScript) Uruchom polecenie `npm install --save azure-storage` w konsoli, aby zainstalować pakiet i zapisać go w pliku **package.json**.</span><span class="sxs-lookup"><span data-stu-id="1e349-151">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="1e349-152">Wykonanie tej operacji może potrwać minutę lub dwie.</span><span class="sxs-lookup"><span data-stu-id="1e349-152">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="1e349-153">(JavaScript) Kliknij nazwę funkcji (**GetUploadUrl**) w obszarze nawigacji po lewej stronie, aby wyświetlić funkcję, a następnie zastąp całą zawartość pliku **index.js** zawartością pliku [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="1e349-153">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![Plik index.js po aktualizacji](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="1e349-155">Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.</span><span class="sxs-lookup"><span data-stu-id="1e349-155">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="1e349-156">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1e349-156">Click **Save**.</span></span> <span data-ttu-id="1e349-157">Na panelu dzienników sprawdź, czy funkcja została pomyślnie skompilowana.</span><span class="sxs-lookup"><span data-stu-id="1e349-157">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="1e349-158">Funkcja generuje tak zwany adresu URL sygnatury dostępu współdzielonego (SAS), który jest używany do przekazania pliku do usługi Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="1e349-158">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="1e349-159">Adres URL sygnatury dostępu współdzielonego jest ważny przez krótki okres i umożliwia przekazanie tylko jednego pliku.</span><span class="sxs-lookup"><span data-stu-id="1e349-159">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="1e349-160">Zapoznaj się z dokumentacją usługi Blob Storage, aby uzyskać więcej informacji na temat [korzystania z sygnatur dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span><span class="sxs-lookup"><span data-stu-id="1e349-160">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="1e349-161">Dodawanie zmiennej środowiskowej dla parametrów połączenia magazynu</span><span class="sxs-lookup"><span data-stu-id="1e349-161">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="1e349-162">Utworzona właśnie funkcja wymaga parametrów połączenia dla konta magazynu, aby móc wygenerować adres URL sygnatury dostępu współdzielonego.</span><span class="sxs-lookup"><span data-stu-id="1e349-162">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="1e349-163">Zamiast zapisywać parametry połączenia w kodzie w treści funkcji, można je zapisać w postaci ustawień aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1e349-163">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="1e349-164">Ustawienia aplikacji są dostępne jako zmienne środowiskowe dla wszystkich funkcji w aplikacji funkcji.</span><span class="sxs-lookup"><span data-stu-id="1e349-164">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="1e349-165">W usłudze Cloud Shell wyślij zapytanie o parametry połączenia konta magazynu i zapisz je w zmiennej powłoki bash o nazwie **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="1e349-165">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="1e349-166">Upewnij się, że zmienna została pomyślnie ustawiona.</span><span class="sxs-lookup"><span data-stu-id="1e349-166">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="1e349-167">Utwórz nowe ustawienie aplikacji o nazwie **AZURE_STORAGE_CONNECTION_STRING** przy użyciu wartości zapisanej w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="1e349-167">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="1e349-168">Upewnij się, że dane wyjściowe polecenia zawierają nowe ustawienia aplikacji z poprawnymi wartościami.</span><span class="sxs-lookup"><span data-stu-id="1e349-168">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="1e349-169">Testowanie funkcji bezserwerowej</span><span class="sxs-lookup"><span data-stu-id="1e349-169">Test the serverless function</span></span>

<span data-ttu-id="1e349-170">Oprócz możliwości tworzenia i edytowania funkcji witryna Azure Portal udostępnia również wbudowane narzędzie do ich testowania.</span><span class="sxs-lookup"><span data-stu-id="1e349-170">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="1e349-171">Aby przetestować funkcję bezserwerową HTTP, kliknij kartę **Test** po prawej stronie okna kodu w celu rozwinięcia panelu testu.</span><span class="sxs-lookup"><span data-stu-id="1e349-171">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="1e349-172">Zmień **metodę Http** na **GET**.</span><span class="sxs-lookup"><span data-stu-id="1e349-172">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="1e349-173">W obszarze **Zapytanie** kliknij pozycję *Dodaj parametr*\* i dodaj następujący parametr:</span><span class="sxs-lookup"><span data-stu-id="1e349-173">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="1e349-174">nazwa</span><span class="sxs-lookup"><span data-stu-id="1e349-174">name</span></span>      |  <span data-ttu-id="1e349-175">wartość</span><span class="sxs-lookup"><span data-stu-id="1e349-175">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="1e349-176">filename</span><span class="sxs-lookup"><span data-stu-id="1e349-176">filename</span></span> | <span data-ttu-id="1e349-177">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="1e349-177">image1.jpg</span></span> |

1. <span data-ttu-id="1e349-178">Kliknij przycisk **Uruchom** w panelu testu, aby wysłać żądanie HTTP do funkcji.</span><span class="sxs-lookup"><span data-stu-id="1e349-178">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="1e349-179">Funkcja zwraca w danych wyjściowych adres URL przekazywania.</span><span class="sxs-lookup"><span data-stu-id="1e349-179">The function returns an upload URL in the output.</span></span> <span data-ttu-id="1e349-180">Wykonanie funkcji pojawia się na panelu dzienników.</span><span class="sxs-lookup"><span data-stu-id="1e349-180">The function execution appears in the logs panel.</span></span>

    ![Dziennik z informacjami o pomyślnym przebiegu funkcji](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="1e349-182">Konfigurowanie mechanizmu CORS w aplikacji funkcji</span><span class="sxs-lookup"><span data-stu-id="1e349-182">Configure CORS in the function app</span></span>

<span data-ttu-id="1e349-183">Ponieważ fronton aplikacji jest hostowany w usłudze Blob Storage, ma inną nazwę domeny niż aplikacja funkcji platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="1e349-183">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="1e349-184">Aby kod JavaScript po stronie klienta mógł pomyślnie wywołać utworzoną właśnie funkcję, aplikacja funkcji musi zostać skonfigurowana na potrzeby współużytkowania zasobów między źródłami (CORS).</span><span class="sxs-lookup"><span data-stu-id="1e349-184">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="1e349-185">Na pasku nawigacyjnym po lewej stronie okna aplikacji funkcji kliknij nazwę aplikacji funkcji.</span><span class="sxs-lookup"><span data-stu-id="1e349-185">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="1e349-186">Kliknij pozycję **Funkcje platformy**, aby wyświetlić listę funkcji zaawansowanych.</span><span class="sxs-lookup"><span data-stu-id="1e349-186">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="1e349-187">W obszarze **API** kliknij pozycję **CORS**.</span><span class="sxs-lookup"><span data-stu-id="1e349-187">Under **API**, click **CORS**.</span></span>

    ![Wybieranie mechanizmu CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="1e349-189">Dodaj dozwolone źródło dla adresu URL aplikacji z poprzedniego modułu z pominięciem końcowego znaku **/** (na przykład: `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="1e349-189">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Ustawienia mechanizmu CORS z widocznym dodanym adresem URL bezserwerowej aplikacji internetowej](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="1e349-191">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1e349-191">Click **Save**.</span></span>

1. <span data-ttu-id="1e349-192">C#</span><span class="sxs-lookup"><span data-stu-id="1e349-192">C#</span></span>

   1. <span data-ttu-id="1e349-193">(C#) Przejdź z powrotem do funkcji `GetUploadUrl`, a następnie wybierz kartę **Integracja**.</span><span class="sxs-lookup"><span data-stu-id="1e349-193">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="1e349-194">(C#) W obszarze Wybrane metody HTTP wybierz pozycję **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="1e349-194">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="1e349-195">Powinny zostać wybrane wszystkie metody: **GET**, **POST** i **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="1e349-195">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="1e349-196">Mechanizm CORS korzysta z metody OPTIONS, która nie jest domyślnie wybrana dla funkcji w języku C#.</span><span class="sxs-lookup"><span data-stu-id="1e349-196">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="1e349-197">(C#) Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1e349-197">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="1e349-198">Będąc cały czas w portalu, przejdź do aplikacji funkcji, wybierz kartę **Przegląd**, a następnie kliknij pozycję **Uruchom ponownie**, aby upewnić się, że zmiany mechanizmu CORS zostaną uwzględnione.</span><span class="sxs-lookup"><span data-stu-id="1e349-198">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="1e349-199">Konfigurowanie mechanizmu CORS w ramach konta magazynu</span><span class="sxs-lookup"><span data-stu-id="1e349-199">Configure CORS in the Storage account</span></span>

<span data-ttu-id="1e349-200">Ponieważ aplikacja wykonuje również wywołania w języku JavaScript po stronie klienta do usługi Blob Storage w celu przekazania plików, należy także skonfigurować konto magazynu do obsługi mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="1e349-200">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="1e349-201">Uruchom następujące polecenie, aby zezwolić na przekazywanie plików do konta magazynu ze wszystkich źródeł.</span><span class="sxs-lookup"><span data-stu-id="1e349-201">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="1e349-202">Modyfikowanie aplikacji internetowej na potrzeby przekazywania obrazów</span><span class="sxs-lookup"><span data-stu-id="1e349-202">Modify the web app to upload images</span></span>

<span data-ttu-id="1e349-203">Aplikacja internetowa pobiera ustawienia z pliku o nazwie **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="1e349-203">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="1e349-204">W poniższych krokach utworzysz plik za pomocą usługi Cloud Shell, a następnie ustawisz parametr `window.apiBaseUrl` na adres URL aplikacji funkcji, a parametr `window.blobBaseUrl` na adres URL punktu końcowego usługi Azure Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="1e349-204">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="1e349-205">W usłudze Cloud Shell upewnij się, że bieżący katalog to folder **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="1e349-205">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="1e349-206">Wyślij zapytanie o adres URL aplikacji funkcji i zapisz go w zmiennej powłoki bash o nazwie **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="1e349-206">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="1e349-207">Upewnij się, że zmienna jest prawidłowo ustawiona.</span><span class="sxs-lookup"><span data-stu-id="1e349-207">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="1e349-208">Aby ustawić podstawowy identyfikator URI wywołań interfejsu API do aplikacji funkcji, utwórz plik **settings.js** i dodaj do niego adres URL aplikacji funkcji, jak pokazano poniżej.</span><span class="sxs-lookup"><span data-stu-id="1e349-208">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="1e349-209">Tę zmianę możesz wprowadzić, uruchamiając następujące polecenie lub korzystając z wierszowego edytora poleceń, na przykład VIM.</span><span class="sxs-lookup"><span data-stu-id="1e349-209">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="1e349-210">Upewnij się, że plik został pomyślnie zapisany.</span><span class="sxs-lookup"><span data-stu-id="1e349-210">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="1e349-211">Wyślij zapytanie o podstawowy adres URL usługi Blob Storage i zapisz go w zmiennej powłoki bash, o nazwie **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="1e349-211">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="1e349-212">Upewnij się, że zmienna jest prawidłowo ustawiona.</span><span class="sxs-lookup"><span data-stu-id="1e349-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="1e349-213">Aby ustawić podstawowy identyfikator URI wywołań interfejsu API do aplikacji funkcji, dołącz adres URL magazynu do pliku **settings.js** w sposób pokazany w następującym wierszu kodu.</span><span class="sxs-lookup"><span data-stu-id="1e349-213">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="1e349-214">Tę zmianę możesz wprowadzić, uruchamiając następujące polecenie lub korzystając z wierszowego edytora poleceń, na przykład VIM.</span><span class="sxs-lookup"><span data-stu-id="1e349-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="1e349-215">Upewnij się, że plik został pomyślnie zapisany oraz że zawiera teraz 2 wiersze.</span><span class="sxs-lookup"><span data-stu-id="1e349-215">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="1e349-216">Przekaż plik do usługi Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="1e349-216">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="1e349-217">Testowanie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="1e349-217">Test the web application</span></span>

<span data-ttu-id="1e349-218">Na tym etapie za pomocą aplikacji galerii można przekazać obraz do usługi Blob Storage, ale nie można jeszcze wyświetlać obrazów.</span><span class="sxs-lookup"><span data-stu-id="1e349-218">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="1e349-219">Aplikacja podejmie próbę wywołania funkcji `GetImages`, która jeszcze nie istnieje, ponieważ utworzysz ją dopiero w jednym z następnych modułów.</span><span class="sxs-lookup"><span data-stu-id="1e349-219">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="1e349-220">Wywołanie nie powiedzie się i zostanie wyświetlona strona internetowa z komunikatem „Analizowanie...”, ale wybrany obraz zostanie pomyślnie przekazany.</span><span class="sxs-lookup"><span data-stu-id="1e349-220">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="1e349-221">O pomyślnym przekazaniu obrazu możesz się przekonać, sprawdzając zawartość kontenera **images** w witrynie Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1e349-221">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="1e349-222">W oknie przeglądarki przejdź do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="1e349-222">In a browser window, browse to the application.</span></span> <span data-ttu-id="1e349-223">Wybierz plik obrazu i przekaż go.</span><span class="sxs-lookup"><span data-stu-id="1e349-223">Select an image file and upload it.</span></span> <span data-ttu-id="1e349-224">Przekazywanie zostaje zakończone, ale ponieważ nie dodaliśmy jeszcze możliwości wyświetlania obrazów, aplikacja nie pokazuje przekazanego zdjęcia.</span><span class="sxs-lookup"><span data-stu-id="1e349-224">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="1e349-225">Strona internetowa wydaje się być zawieszona z komunikatem „Analizowanie obrazu...”. Naprawisz to później.</span><span class="sxs-lookup"><span data-stu-id="1e349-225">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="1e349-226">W usłudze Cloud Shell upewnij się, że obraz został przekazany do kontenera **images**.</span><span class="sxs-lookup"><span data-stu-id="1e349-226">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="1e349-227">Przed przejściem do następnego samouczka usuń wszystkie pliki z kontenera **images**.</span><span class="sxs-lookup"><span data-stu-id="1e349-227">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="1e349-228">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="1e349-228">Summary</span></span>

<span data-ttu-id="1e349-229">W tym module utworzono aplikację funkcji platformy Azure i przedstawiono sposób użycia funkcji bezserwerowej w celu umożliwienia aplikacji internetowej przekazania obrazów do usługi Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="1e349-229">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="1e349-230">Dalej dowiesz się, jak utworzyć miniatury przekazanych obrazów przy użyciu funkcji bezserwerowej wyzwalanej za pomocą obiektu Blob.</span><span class="sxs-lookup"><span data-stu-id="1e349-230">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
