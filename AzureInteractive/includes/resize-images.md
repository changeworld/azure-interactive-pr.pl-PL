---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460089"
---
<span data-ttu-id="1fc84-103">W poprzednim module przedstawiono, jak funkcja bezserwerowa może ułatwić bezpieczne przekazywanie obrazów do magazynu obiektów blob z aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="1fc84-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="1fc84-104">W tym module utworzysz kolejną funkcję bezserwerową, aby obserwować przekazywane obrazy i tworzyć dla nich miniatury.</span><span class="sxs-lookup"><span data-stu-id="1fc84-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="1fc84-105">Tworzenie kontenera usługi Blob Storage do przechowywania miniatur</span><span class="sxs-lookup"><span data-stu-id="1fc84-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="1fc84-106">Obrazy o pełnym rozmiarze są przechowywane w kontenerze o nazwie **images**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="1fc84-107">Potrzebujesz kolejnego kontenera do przechowywania miniatur tych obrazów.</span><span class="sxs-lookup"><span data-stu-id="1fc84-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="1fc84-108">Upewnij się, że jesteś zalogowanym użytkownikiem usługi Cloud Shell (w powłoce bash).</span><span class="sxs-lookup"><span data-stu-id="1fc84-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="1fc84-109">Jeśli nie, wybierz pozycję **Wejdź w tryb koncentracji uwagi**, aby otworzyć okno usługi Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="1fc84-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="1fc84-110">Korzystając ze swojego konta magazynu, utwórz nowy kontener o nazwie **thumbnails** z publicznym dostępem do wszystkich obiektów blob.</span><span class="sxs-lookup"><span data-stu-id="1fc84-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="1fc84-111">Tworzenie bezserwerowej funkcji wyzwalanej przez obiekty blob</span><span class="sxs-lookup"><span data-stu-id="1fc84-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="1fc84-112">Wyzwalacz definiuje sposób wywoływania funkcji.</span><span class="sxs-lookup"><span data-stu-id="1fc84-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="1fc84-113">Funkcja, którą utworzysz następnie, używa wyzwalacza obiektów blob: funkcja jest automatycznie wywoływana, gdy obiekt blob (plik obrazu) jest przekazywany do kontenera **images**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="1fc84-114">Funkcja musi mieć jeden wyzwalacz.</span><span class="sxs-lookup"><span data-stu-id="1fc84-114">A function must have one trigger.</span></span> <span data-ttu-id="1fc84-115">Wyzwalacze mają skojarzone dane będące zwykle ładunkiem, który wyzwolił funkcję.</span><span class="sxs-lookup"><span data-stu-id="1fc84-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="1fc84-116">Powiązania definiują sposób odczytu i zapisu danych przez funkcję na platformie Azure lub w usługach innych firm.</span><span class="sxs-lookup"><span data-stu-id="1fc84-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="1fc84-117">Ta funkcja tworzy wersję miniatury obrazu, który ją wyzwolił, i zapisuje miniaturę w kontenerze *thumbnails*.</span><span class="sxs-lookup"><span data-stu-id="1fc84-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="1fc84-118">Otwórz aplikację funkcji w witrynie Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="1fc84-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="1fc84-119">W obszarze nawigacji po lewej stronie okna aplikacji funkcji umieść kursor nad opcją **Funkcje** i kliknij znak **+**, aby rozpocząć tworzenie nowej funkcji bezserwerowej.</span><span class="sxs-lookup"><span data-stu-id="1fc84-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="1fc84-120">Jeżeli zostanie wyświetlona strona przewodnika Szybki start, kliknij opcję **Funkcja niestandardowa**, aby zobaczyć listę szablonów funkcji.</span><span class="sxs-lookup"><span data-stu-id="1fc84-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="1fc84-121">Znajdź szablon **BlobTrigger** i wybierz go.</span><span class="sxs-lookup"><span data-stu-id="1fc84-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="1fc84-122">Użyj tych wartości, aby utworzyć funkcję, która tworzy miniatury podczas przekazywania obrazów.</span><span class="sxs-lookup"><span data-stu-id="1fc84-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="1fc84-123">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="1fc84-123">Setting</span></span>      |  <span data-ttu-id="1fc84-124">Sugerowana wartość</span><span class="sxs-lookup"><span data-stu-id="1fc84-124">Suggested value</span></span>   | <span data-ttu-id="1fc84-125">Opis</span><span class="sxs-lookup"><span data-stu-id="1fc84-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="1fc84-126">**Język**</span><span class="sxs-lookup"><span data-stu-id="1fc84-126">**Language**</span></span> | <span data-ttu-id="1fc84-127">C# lub JavaScript</span><span class="sxs-lookup"><span data-stu-id="1fc84-127">C# or JavaScript</span></span> | <span data-ttu-id="1fc84-128">Wybierz preferowany język.</span><span class="sxs-lookup"><span data-stu-id="1fc84-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="1fc84-129">**Nazwa funkcji**</span><span class="sxs-lookup"><span data-stu-id="1fc84-129">**Name your function**</span></span> | <span data-ttu-id="1fc84-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="1fc84-130">ResizeImage</span></span> | <span data-ttu-id="1fc84-131">Wpisz tę nazwę dokładnie tak, jak pokazano, aby aplikacja mogła odnaleźć funkcję.</span><span class="sxs-lookup"><span data-stu-id="1fc84-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="1fc84-132">**Ścieżka**</span><span class="sxs-lookup"><span data-stu-id="1fc84-132">**Path**</span></span> | <span data-ttu-id="1fc84-133">images/{nazwa}</span><span class="sxs-lookup"><span data-stu-id="1fc84-133">images/{name}</span></span> | <span data-ttu-id="1fc84-134">Wykonaj funkcję, gdy plik pojawi się w kontenerze **images**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="1fc84-135">**Informacje o koncie magazynu**</span><span class="sxs-lookup"><span data-stu-id="1fc84-135">**Storage account information**</span></span> | <span data-ttu-id="1fc84-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="1fc84-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="1fc84-137">Użyj zmiennej środowiskowej utworzonej poprzednio z parametrami połączenia.</span><span class="sxs-lookup"><span data-stu-id="1fc84-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Wprowadzanie ustawień dla nowej funkcji](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="1fc84-139">Kliknij przycisk **Utwórz**, aby utworzyć funkcję.</span><span class="sxs-lookup"><span data-stu-id="1fc84-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="1fc84-140">Po utworzeniu funkcji kliknij przycisk **Integruj**, aby wyświetlić jej wyzwalacz oraz powiązania wejściowe i wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="1fc84-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="1fc84-141">Kliknij przycisk **Nowe dane wyjściowe**, aby utworzyć nowe powiązanie wyjściowe.</span><span class="sxs-lookup"><span data-stu-id="1fc84-141">Click **New output** to create a new output binding.</span></span>

    ![Wybieranie opcji Nowe dane wyjściowe na karcie Integracja](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="1fc84-143">Wybierz pozycję **Azure Blob Storage**, a następnie kliknij przycisk **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="1fc84-144">Może być konieczne przewinięcie w dół w celu wyświetlenia przycisku **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Wybieranie pozycji Azure Blob Storage](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="1fc84-146">Wprowadź następujące wartości.</span><span class="sxs-lookup"><span data-stu-id="1fc84-146">Enter the following values.</span></span>

    | <span data-ttu-id="1fc84-147">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="1fc84-147">Setting</span></span>      |  <span data-ttu-id="1fc84-148">Sugerowana wartość</span><span class="sxs-lookup"><span data-stu-id="1fc84-148">Suggested value</span></span>   | <span data-ttu-id="1fc84-149">Opis</span><span class="sxs-lookup"><span data-stu-id="1fc84-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="1fc84-150">**Nazwa parametru obiektu blob**</span><span class="sxs-lookup"><span data-stu-id="1fc84-150">**Blob parameter name**</span></span> | <span data-ttu-id="1fc84-151">miniatura</span><span class="sxs-lookup"><span data-stu-id="1fc84-151">thumbnail</span></span> | <span data-ttu-id="1fc84-152">Funkcja używa parametru z tą nazwą do zapisania miniatury.</span><span class="sxs-lookup"><span data-stu-id="1fc84-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="1fc84-153">**Użyj wartości zwracanej funkcji**</span><span class="sxs-lookup"><span data-stu-id="1fc84-153">**Use function return value**</span></span> | <span data-ttu-id="1fc84-154">Nie</span><span class="sxs-lookup"><span data-stu-id="1fc84-154">No</span></span> |  |
    | <span data-ttu-id="1fc84-155">**Ścieżka**</span><span class="sxs-lookup"><span data-stu-id="1fc84-155">**Path**</span></span> | <span data-ttu-id="1fc84-156">thumbnails/{nazwa}</span><span class="sxs-lookup"><span data-stu-id="1fc84-156">thumbnails/{name}</span></span> | <span data-ttu-id="1fc84-157">Miniatury są zapisywane w kontenerze o nazwie **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="1fc84-158">**Połączenie konta magazynu**</span><span class="sxs-lookup"><span data-stu-id="1fc84-158">**Storage account connection**</span></span> | <span data-ttu-id="1fc84-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="1fc84-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="1fc84-160">Użyj zmiennej środowiskowej utworzonej poprzednio z parametrami połączenia.</span><span class="sxs-lookup"><span data-stu-id="1fc84-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Wprowadzanie ustawień powiązania wyjściowego obiektu blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="1fc84-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="1fc84-162">**JavaScript**</span></span>

    1. <span data-ttu-id="1fc84-163">(JavaScript) Kliknij opcję **Edytor zaawansowany** w prawym górnym rogu okna, aby wyświetlić kod JSON reprezentujący powiązania.</span><span class="sxs-lookup"><span data-stu-id="1fc84-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="1fc84-164">(JavaScript) W powiązaniu `blobTrigger` dodaj właściwość o nazwie `dataType` z wartością `binary`.</span><span class="sxs-lookup"><span data-stu-id="1fc84-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="1fc84-165">Umożliwi to skonfigurowanie powiązania w celu przekazania zawartości obiektu blob do funkcji w postaci danych binarnych.</span><span class="sxs-lookup"><span data-stu-id="1fc84-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. <span data-ttu-id="1fc84-166">Kliknij przycisk **Zapisz**, aby utworzyć nowe powiązanie.</span><span class="sxs-lookup"><span data-stu-id="1fc84-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="1fc84-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="1fc84-167">**C#**</span></span>

    1. <span data-ttu-id="1fc84-168">(C#) Wybierz nazwę funkcji **ResizeImage** na lewym pasku nawigacyjnym, aby otworzyć kod źródłowy funkcji.</span><span class="sxs-lookup"><span data-stu-id="1fc84-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="1fc84-169">(C#) Funkcja wymaga pakietu NuGet o nazwie **ImageResizer** w celu generowania miniatur.</span><span class="sxs-lookup"><span data-stu-id="1fc84-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="1fc84-170">Pakiety NuGet są dodawane do funkcji C# przy użyciu pliku **project.json**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="1fc84-171">Aby utworzyć plik, kliknij przycisk **Wyświetl pliki** po prawej stronie w celu wyświetlenia plików tworzących funkcję.</span><span class="sxs-lookup"><span data-stu-id="1fc84-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="1fc84-172">(C#) Kliknij przycisk **Dodaj**, aby dodać nowy plik o nazwie **project.json**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="1fc84-173">(C#) Skopiuj zawartość pliku [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) do nowo utworzonego pliku.</span><span class="sxs-lookup"><span data-stu-id="1fc84-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="1fc84-174">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="1fc84-174">Save the file.</span></span> <span data-ttu-id="1fc84-175">Pakiety zostaną automatycznie przywrócone w przypadku zaktualizowania pliku.</span><span class="sxs-lookup"><span data-stu-id="1fc84-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![Plik project.json z pakietem ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="1fc84-177">(C#) Wybierz plik **run.csx** w obszarze **Wyświetl pliki** i zastąp jego zawartość zawartością pliku [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="1fc84-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="1fc84-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="1fc84-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="1fc84-179">(JavaScript) Ta funkcja wymaga pakietu `jimp` z programu npm w celu zmiany rozmiaru zdjęcia.</span><span class="sxs-lookup"><span data-stu-id="1fc84-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="1fc84-180">Aby zainstalować pakiet npm, kliknij nazwę aplikacji funkcji w lewym obszarze nawigacji i kliknij pozycję **Funkcje platformy**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="1fc84-181">(JavaScript) Kliknij pozycję **Konsola**, aby wyświetlić okno konsoli.</span><span class="sxs-lookup"><span data-stu-id="1fc84-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="1fc84-182">(JavaScript) Uruchom polecenie `npm install jimp` w konsoli.</span><span class="sxs-lookup"><span data-stu-id="1fc84-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="1fc84-183">Wykonanie tej operacji może potrwać minutę lub dwie.</span><span class="sxs-lookup"><span data-stu-id="1fc84-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="1fc84-184">(JavaScript) Kliknij nazwę funkcji **ResizeImage** na lewym pasku nawigacyjnym, aby wyświetlić funkcję, a następnie zastąp całą zawartość pliku **index.js** zawartością pliku [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="1fc84-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="1fc84-185">Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.</span><span class="sxs-lookup"><span data-stu-id="1fc84-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="1fc84-186">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-186">Click **Save**.</span></span> <span data-ttu-id="1fc84-187">Sprawdź panel dzienników, aby upewnić się, że funkcja została prawidłowo zapisana oraz że nie wystąpiły błędy.</span><span class="sxs-lookup"><span data-stu-id="1fc84-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="1fc84-188">Testowanie funkcji bezserwerowej</span><span class="sxs-lookup"><span data-stu-id="1fc84-188">Test the serverless function</span></span>

1. <span data-ttu-id="1fc84-189">Otwórz aplikację w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="1fc84-189">Open the application in a browser.</span></span> <span data-ttu-id="1fc84-190">Wybierz plik obrazu i przekaż go.</span><span class="sxs-lookup"><span data-stu-id="1fc84-190">Select an image file and upload it.</span></span> <span data-ttu-id="1fc84-191">Przekazywanie zostaje zakończone, ale ponieważ nie dodaliśmy jeszcze możliwości wyświetlania obrazów, aplikacja nie pokazuje przekazanego zdjęcia.</span><span class="sxs-lookup"><span data-stu-id="1fc84-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="1fc84-192">W usłudze Cloud Shell upewnij się, że obraz został przekazany do kontenera **images**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="1fc84-193">Upewnij się, że miniatura została utworzona w kontenerze o nazwie **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="1fc84-194">Uzyskaj adres URL miniatury.</span><span class="sxs-lookup"><span data-stu-id="1fc84-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="1fc84-195">Otwórz adres URL w przeglądarce i upewnij się, że miniatura została utworzona prawidłowo.</span><span class="sxs-lookup"><span data-stu-id="1fc84-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="1fc84-196">Przed przejściem do następnego samouczka należy usunąć wszystkie pliki z kontenerów **images** i **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="1fc84-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="1fc84-197">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="1fc84-197">Summary</span></span>

<span data-ttu-id="1fc84-198">W tym module utworzono funkcję bezserwerową, aby tworzyć miniaturę za każdym razem, gdy obraz jest przekazywany do kontenera magazynu obiektów blob.</span><span class="sxs-lookup"><span data-stu-id="1fc84-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="1fc84-199">Następnie dowiesz się, jak używać usługi Azure Cosmos DB do przechowywania i wyświetlania listy metadanych obrazu.</span><span class="sxs-lookup"><span data-stu-id="1fc84-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
