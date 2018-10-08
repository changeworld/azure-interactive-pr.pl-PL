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
ms.openlocfilehash: 194a25dbf9abda80379aa5aab408ac4ffe9ab7f5
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460062"
---
<span data-ttu-id="09858-103">Azure Cosmos DB to dostępna w skali światowej, bezserwerowa, wielomodelowa baza danych firmy Microsoft.</span><span class="sxs-lookup"><span data-stu-id="09858-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="09858-104">W tym module nauczysz się, jak używać usługi Azure Functions do przechowywania i pobierania metadanych obrazu w formie dokumentów JSON w usłudze Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="09858-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="09858-105">Tworzenie konta, bazy danych i kolekcji usługi Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="09858-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="09858-106">Konto usługi Cosmos DB to zasób platformy Azure, który zawiera bazy danych usługi Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="09858-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="09858-107">Upewnij się, że użytkownik jest nadal zalogowany w usłudze Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="09858-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="09858-108">Jeśli nie, wybierz pozycję **Wejdź w tryb koncentracji uwagi**, aby otworzyć okno usługi Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="09858-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="09858-109">Utwórz konto usługi Cosmos DB o unikatowej nazwie w tej samej grupie zasobów co inne zasoby w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="09858-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="09858-110">Po utworzeniu konta usługi Cosmos DB utwórz nową bazę danych o nazwie **imagesdb** na koncie.</span><span class="sxs-lookup"><span data-stu-id="09858-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="09858-111">Po utworzeniu bazy danych utwórz nową kolekcję o nazwie **images** w bazie danych przy przepływności równej 400 jednostek żądań (RU).</span><span class="sxs-lookup"><span data-stu-id="09858-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="09858-112">Zapisywanie dokumentu do usługi Cosmos DB w momencie utworzenia miniatury</span><span class="sxs-lookup"><span data-stu-id="09858-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="09858-113">Powiązanie wyjściowe usługi Cosmos DB umożliwia tworzenie dokumentów w kolekcji usługi Cosmos DB z poziomu usługi Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="09858-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="09858-114">W poniższych krokach skonfigurujesz powiązanie wyjściowe usługi Cosmos DB w funkcji **ResizeImage** oraz zmodyfikujesz funkcję, aby zwracała dokument (obiekt) do zapisania.</span><span class="sxs-lookup"><span data-stu-id="09858-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="09858-115">Otwórz aplikację funkcji w witrynie Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="09858-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="09858-116">W obszarze nawigacji po lewej stronie ekranu rozwiń funkcję **ResizeImage**, a następnie wybierz opcję **Integruj**.</span><span class="sxs-lookup"><span data-stu-id="09858-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="09858-117">W obszarze **Dane wyjściowe** kliknij przycisk **Nowe dane wyjściowe**.</span><span class="sxs-lookup"><span data-stu-id="09858-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="09858-118">Znajdź element **Azure Cosmos DB** i wybierz go.</span><span class="sxs-lookup"><span data-stu-id="09858-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="09858-119">Następnie kliknij pozycję **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="09858-119">Then click **Select**.</span></span>

    ![Wybieranie nowych danych wyjściowych](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="09858-121">Wypełnij pola w pozycji **Dane wyjściowe usługi Azure Cosmos DB** przy użyciu następujących wartości.</span><span class="sxs-lookup"><span data-stu-id="09858-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="09858-122">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="09858-122">Setting</span></span>      |  <span data-ttu-id="09858-123">Sugerowana wartość</span><span class="sxs-lookup"><span data-stu-id="09858-123">Suggested value</span></span>   | <span data-ttu-id="09858-124">Opis</span><span class="sxs-lookup"><span data-stu-id="09858-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="09858-125">**Nazwa parametru dokumentu**</span><span class="sxs-lookup"><span data-stu-id="09858-125">**Document parameter name**</span></span> | <span data-ttu-id="09858-126">Wybieranie opcji **Użyj wartości zwracanej funkcji**</span><span class="sxs-lookup"><span data-stu-id="09858-126">Select **Use function return value**</span></span> | <span data-ttu-id="09858-127">Wartość pola tekstowego jest automatycznie ustawiana na **$return**.</span><span class="sxs-lookup"><span data-stu-id="09858-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="09858-128">**Nazwa bazy danych**</span><span class="sxs-lookup"><span data-stu-id="09858-128">**Database name**</span></span> | <span data-ttu-id="09858-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="09858-129">imagesdb</span></span> | <span data-ttu-id="09858-130">Użyj nazwy utworzonej bazy danych.</span><span class="sxs-lookup"><span data-stu-id="09858-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="09858-131">**Nazwa kolekcji**</span><span class="sxs-lookup"><span data-stu-id="09858-131">**Collection name**</span></span> | <span data-ttu-id="09858-132">images</span><span class="sxs-lookup"><span data-stu-id="09858-132">images</span></span> | <span data-ttu-id="09858-133">Użyj nazwy utworzonej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="09858-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="09858-134">Obok pozycji **Połączenie konta usługi Azure Cosmos DB** kliknij opcję **nowe**.</span><span class="sxs-lookup"><span data-stu-id="09858-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="09858-135">Wybierz wcześniej utworzone konto usługi Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="09858-135">Select the Cosmos DB account you previously created.</span></span>

    ![Wprowadzanie ustawień dla powiązania wyjściowego usługi Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="09858-137">Kliknij przycisk **Zapisz**, aby utworzyć powiązanie wyjściowe usługi Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="09858-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="09858-138">Kliknij nazwę funkcji **ResizeImage** po lewej stronie, aby otworzyć funkcję.</span><span class="sxs-lookup"><span data-stu-id="09858-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="09858-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="09858-139">**C#**</span></span>

    1. <span data-ttu-id="09858-140">(C#) Zmień zwracany typ funkcji z metody **void** na **obiekt**.</span><span class="sxs-lookup"><span data-stu-id="09858-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="09858-141">(C#) Na końcu funkcji dodaj następujący blok kodu, aby zwrócić dokument do zapisania:</span><span class="sxs-lookup"><span data-stu-id="09858-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![Plik run.csx dla funkcji ResizeImages po wprowadzeniu modyfikacji](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="09858-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="09858-143">**JavaScript**</span></span>

    1. <span data-ttu-id="09858-144">(JavaScript) Zmień instrukcję `context.done()` w klauzuli `else`, aby zwrócić dokument do zapisania w usłudze Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="09858-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. <span data-ttu-id="09858-145">Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.</span><span class="sxs-lookup"><span data-stu-id="09858-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="09858-146">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="09858-146">Click **Save**.</span></span> <span data-ttu-id="09858-147">Sprawdź panel dzienników, aby upewnić się, że funkcja została prawidłowo zapisana oraz że nie wystąpiły błędy.</span><span class="sxs-lookup"><span data-stu-id="09858-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="09858-148">Tworzenie funkcji w celu wyświetlenia listy obrazów z usługi Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="09858-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="09858-149">Aplikacja internetowa wymaga interfejsu API, aby pobierać metadane obrazów z usługi Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="09858-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="09858-150">W poniższych krokach</span><span class="sxs-lookup"><span data-stu-id="09858-150">In the following steps.</span></span> <span data-ttu-id="09858-151">utworzysz funkcję z wyzwalaniem HTTP, która będzie używać powiązania wejściowego usługi Cosmos DB w celu wysyłania zapytań do kolekcji bazy danych.</span><span class="sxs-lookup"><span data-stu-id="09858-151">you create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="09858-152">W aplikacji funkcji umieść kursor nad pozycją **Funkcje** po lewej stronie i kliknij znak **+**, aby utworzyć nową funkcję.</span><span class="sxs-lookup"><span data-stu-id="09858-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="09858-153">Znajdź szablon **HttpTrigger** i wybierz go.</span><span class="sxs-lookup"><span data-stu-id="09858-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="09858-154">Użyj następujących wartości, aby utworzyć funkcję, która generuje adresy URL obrazów.</span><span class="sxs-lookup"><span data-stu-id="09858-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="09858-155">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="09858-155">Setting</span></span>      |  <span data-ttu-id="09858-156">Sugerowana wartość</span><span class="sxs-lookup"><span data-stu-id="09858-156">Suggested value</span></span>   | <span data-ttu-id="09858-157">Opis</span><span class="sxs-lookup"><span data-stu-id="09858-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="09858-158">**Nazwa funkcji**</span><span class="sxs-lookup"><span data-stu-id="09858-158">**Name your function**</span></span> | <span data-ttu-id="09858-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="09858-159">GetImages</span></span> | <span data-ttu-id="09858-160">Wpisz tę nazwę dokładnie tak, jak pokazano, aby aplikacja mogła odnaleźć funkcję.</span><span class="sxs-lookup"><span data-stu-id="09858-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="09858-161">**Poziom autoryzacji**</span><span class="sxs-lookup"><span data-stu-id="09858-161">**Authorization level**</span></span> | <span data-ttu-id="09858-162">Anonimowe</span><span class="sxs-lookup"><span data-stu-id="09858-162">Anonymous</span></span> | <span data-ttu-id="09858-163">Zezwalaj na publiczny dostęp do funkcji.</span><span class="sxs-lookup"><span data-stu-id="09858-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="09858-164">Kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="09858-164">Click **Create**.</span></span>

1. <span data-ttu-id="09858-165">Po utworzeniu nowej funkcji kliknij opcję **Integruj** pod nazwą funkcji na lewym pasku nawigacyjnym.</span><span class="sxs-lookup"><span data-stu-id="09858-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="09858-166">Kliknij przycisk **Nowe dane wejściowe** i wybierz opcję **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="09858-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Wybieranie nowych danych wejściowych](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="09858-168">Kliknij pozycję **Wybierz**.</span><span class="sxs-lookup"><span data-stu-id="09858-168">Click **Select**.</span></span>

1. <span data-ttu-id="09858-169">Wypełnij następujące wartości:</span><span class="sxs-lookup"><span data-stu-id="09858-169">Fill out the following values:</span></span>

    | <span data-ttu-id="09858-170">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="09858-170">Setting</span></span>      |  <span data-ttu-id="09858-171">Sugerowana wartość</span><span class="sxs-lookup"><span data-stu-id="09858-171">Suggested value</span></span>   | <span data-ttu-id="09858-172">Opis</span><span class="sxs-lookup"><span data-stu-id="09858-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="09858-173">**Nazwa parametru dokumentu**</span><span class="sxs-lookup"><span data-stu-id="09858-173">**Document parameter name**</span></span> | <span data-ttu-id="09858-174">documents</span><span class="sxs-lookup"><span data-stu-id="09858-174">documents</span></span> | <span data-ttu-id="09858-175">Pasuje do nazwy parametru w funkcji.</span><span class="sxs-lookup"><span data-stu-id="09858-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="09858-176">**Nazwa bazy danych**</span><span class="sxs-lookup"><span data-stu-id="09858-176">**Database name**</span></span> | <span data-ttu-id="09858-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="09858-177">imagesdb</span></span> |  |
    | <span data-ttu-id="09858-178">**Nazwa kolekcji**</span><span class="sxs-lookup"><span data-stu-id="09858-178">**Collection name**</span></span> | <span data-ttu-id="09858-179">images</span><span class="sxs-lookup"><span data-stu-id="09858-179">images</span></span> |  |
    | <span data-ttu-id="09858-180">**Zapytanie SQL**</span><span class="sxs-lookup"><span data-stu-id="09858-180">**SQL query**</span></span> | <span data-ttu-id="09858-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="09858-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="09858-182">Pobierz dokumenty, zaczynając od najnowszego dokumentu.</span><span class="sxs-lookup"><span data-stu-id="09858-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="09858-183">**Połączenie konta usługi Azure Cosmos DB**</span><span class="sxs-lookup"><span data-stu-id="09858-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="09858-184">Wybieranie istniejących parametrów połączenia</span><span class="sxs-lookup"><span data-stu-id="09858-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="09858-185">Kliknij przycisk **Zapisz**, aby utworzyć powiązanie wejściowe.</span><span class="sxs-lookup"><span data-stu-id="09858-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="09858-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="09858-186">**C#**</span></span>

    1. <span data-ttu-id="09858-187">Kliknij nazwę funkcji, aby otworzyć okno kodu, a następnie zastąp całą zawartość pliku **run.csx** zawartością pliku [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span><span class="sxs-lookup"><span data-stu-id="09858-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="09858-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="09858-188">**JavaScript**</span></span>

    1. <span data-ttu-id="09858-189">Kliknij nazwę funkcji, aby otworzyć okno kodu, a następnie zastąp całą zawartość pliku **index.js** zawartością pliku [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span><span class="sxs-lookup"><span data-stu-id="09858-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="09858-190">Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.</span><span class="sxs-lookup"><span data-stu-id="09858-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="09858-191">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="09858-191">Click **Save**.</span></span> <span data-ttu-id="09858-192">Sprawdź panel dzienników, aby upewnić się, że funkcja została prawidłowo zapisana oraz że nie wystąpiły błędy.</span><span class="sxs-lookup"><span data-stu-id="09858-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="09858-193">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="09858-193">Test the application</span></span>

1. <span data-ttu-id="09858-194">Otwórz aplikację w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="09858-194">Open the application in a browser.</span></span> <span data-ttu-id="09858-195">Wybierz plik obrazu i przekaż go.</span><span class="sxs-lookup"><span data-stu-id="09858-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="09858-196">Po kilku sekundach miniatura nowego obrazu pojawi się na stronie.</span><span class="sxs-lookup"><span data-stu-id="09858-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="09858-197">W witrynie Azure Portal użyj pola wyszukiwania, aby wyszukać konto usługi Cosmos DB według nazwy.</span><span class="sxs-lookup"><span data-stu-id="09858-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="09858-198">Kliknij konto, aby je otworzyć.</span><span class="sxs-lookup"><span data-stu-id="09858-198">Click it to open it.</span></span>

1. <span data-ttu-id="09858-199">Kliknij przycisk **Eksplorator danych** po lewej stronie, aby przeglądać kolekcje i dokumenty.</span><span class="sxs-lookup"><span data-stu-id="09858-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="09858-200">W obszarze bazy danych **imagesdb** zaznacz kolekcję **images**.</span><span class="sxs-lookup"><span data-stu-id="09858-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="09858-201">Upewnij się, że dokument został utworzony dla przekazanego obrazu.</span><span class="sxs-lookup"><span data-stu-id="09858-201">Confirm that a document was created for the uploaded image.</span></span>

    ![Eksplorator danych przedstawiający dokument dla przekazanego obrazu](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="09858-203">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="09858-203">Summary</span></span>

<span data-ttu-id="09858-204">W tym module przedstawiono sposób tworzenia konta, bazy danych i kolekcji usługi Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="09858-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="09858-205">Przedstawiono również sposób używania powiązań usługi Cosmos DB w celu zapisywania i pobierania metadanych obrazu w kolekcji usługi Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="09858-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="09858-206">Następnie dowiesz się, jak automatycznie generować podpis dla każdego przekazanego obrazu przy użyciu usług Microsoft Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="09858-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>
