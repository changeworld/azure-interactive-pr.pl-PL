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
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460006"
---
<span data-ttu-id="c2049-103">Na tym etapie aplikacja jest funkcjonalną galerią, która umożliwia przekazywanie i wyświetlanie obrazów.</span><span class="sxs-lookup"><span data-stu-id="c2049-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="c2049-104">W tym module dowiesz się, jak używać interfejsu API przetwarzania obrazów usług Microsoft Cognitive Services w celu wygenerowania podpisów dla przekazanych obrazów oraz jak zapisać podpisy w metadanych obrazów w usłudze Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="c2049-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="c2049-105">Tworzenie konta funkcji przetwarzania obrazów</span><span class="sxs-lookup"><span data-stu-id="c2049-105">Create a Computer Vision account</span></span>

<span data-ttu-id="c2049-106">Usługi Microsoft Cognitive Services to kolekcja usług dostępna dla deweloperów, która umożliwia im zwiększenie inteligencji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c2049-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="c2049-107">Interfejs API przetwarzania obrazów to bezserwerowa usługa, która przetwarza obrazy przy użyciu zaawansowanych algorytmów i zwraca informacje o każdym obrazie.</span><span class="sxs-lookup"><span data-stu-id="c2049-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="c2049-108">Ma warstwę bezpłatną, która zapewnia do 5000 wywołań interfejsu API w miesiącu.</span><span class="sxs-lookup"><span data-stu-id="c2049-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="c2049-109">Upewnij się, że użytkownik jest nadal zalogowany w usłudze Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="c2049-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="c2049-110">Jeśli nie, wybierz pozycję **Wejdź w tryb koncentracji uwagi**, aby otworzyć okno usługi Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="c2049-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="c2049-111">Utwórz nowe konto usługi Cognitive Services typu **ComputerVision** z unikatową nazwą w grupie zasobów.</span><span class="sxs-lookup"><span data-stu-id="c2049-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="c2049-112">W przypadku warstwy bezpłatnej użyj opcji **F0** jako jednostki SKU.</span><span class="sxs-lookup"><span data-stu-id="c2049-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="c2049-113">Jeśli masz już istniejące konto funkcji przetwarzania obrazów, może być konieczne utworzenie konta standardowego (S1), jednak może to pociągnąć za sobą pewne koszty.</span><span class="sxs-lookup"><span data-stu-id="c2049-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="c2049-114">Tworzenie ustawień aplikacji funkcji na potrzeby adresu URL i klucza funkcji przetwarzania obrazów</span><span class="sxs-lookup"><span data-stu-id="c2049-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="c2049-115">Aby wywołać interfejs API przetwarzania obrazów, wymagany jest adres URL i klucz.</span><span class="sxs-lookup"><span data-stu-id="c2049-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="c2049-116">Uzyskaj klucz i adres URL interfejsu API przetwarzania obrazów, a następnie zapisz je w zmiennych powłoki bash.</span><span class="sxs-lookup"><span data-stu-id="c2049-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="c2049-117">Utwórz ustawienia aplikacji o nazwach **COMP_VISION_KEY** i **COMP_VISION_URL** w aplikacji funkcji.</span><span class="sxs-lookup"><span data-stu-id="c2049-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="c2049-118">Wywoływanie interfejsu API przetwarzania obrazów z funkcji ResizeImage</span><span class="sxs-lookup"><span data-stu-id="c2049-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="c2049-119">W następnych krokach zmodyfikujesz funkcję **ResizeImage**, aby wywołać interfejs API przetwarzania obrazów i opisać każdy przekazany obraz oraz zapisać opis w usłudze Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="c2049-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="c2049-120">Otwórz aplikację funkcji w witrynie Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c2049-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="c2049-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="c2049-121">**C#**</span></span>

    1. <span data-ttu-id="c2049-122">(C#) Przy użyciu panelu nawigacyjnego po lewej stronie znajdź funkcję **ResizeImage** i otwórz jej okno kodu.</span><span class="sxs-lookup"><span data-stu-id="c2049-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="c2049-123">(C#) Zastąp kod zawartością pliku [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span><span class="sxs-lookup"><span data-stu-id="c2049-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="c2049-124">Ten kod używa metody `HttpClient` do wywoływania interfejsu API przetwarzania obrazów i zapisywania wyników funkcji w usłudze Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="c2049-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="c2049-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="c2049-125">**JavaScript**</span></span>

    1. <span data-ttu-id="c2049-126">(JavaScript) Ta funkcja wymaga pakietu `axios` z programu npm w celu wykonywania wywołań HTTP do interfejsu API przetwarzania obrazów.</span><span class="sxs-lookup"><span data-stu-id="c2049-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="c2049-127">Aby zainstalować pakiet npm, kliknij nazwę aplikacji funkcji w lewym obszarze nawigacji i kliknij pozycję **Funkcje platformy**.</span><span class="sxs-lookup"><span data-stu-id="c2049-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="c2049-128">(JavaScript) Kliknij pozycję **Konsola**, aby wyświetlić okno konsoli.</span><span class="sxs-lookup"><span data-stu-id="c2049-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="c2049-129">(JavaScript) Uruchom polecenie `npm install axios` w konsoli.</span><span class="sxs-lookup"><span data-stu-id="c2049-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="c2049-130">Wykonanie tej operacji może potrwać minutę lub dwie.</span><span class="sxs-lookup"><span data-stu-id="c2049-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="c2049-131">(JavaScript) Kliknij nazwę funkcji (**ResizeImage**) na lewym pasku nawigacyjnym, aby wyświetlić funkcję, a następnie zastąp całą zawartość pliku **index.js** zawartością pliku [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span><span class="sxs-lookup"><span data-stu-id="c2049-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="c2049-132">Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.</span><span class="sxs-lookup"><span data-stu-id="c2049-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="c2049-133">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="c2049-133">Click **Save**.</span></span> <span data-ttu-id="c2049-134">Sprawdź panel dzienników, aby upewnić się, że funkcja została prawidłowo zapisana oraz że nie wystąpiły błędy.</span><span class="sxs-lookup"><span data-stu-id="c2049-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="c2049-135">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="c2049-135">Test the application</span></span>

1. <span data-ttu-id="c2049-136">Otwórz aplikację w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="c2049-136">Open the application in a browser.</span></span> <span data-ttu-id="c2049-137">Wybierz plik obrazu, a następnie go przekaż.</span><span class="sxs-lookup"><span data-stu-id="c2049-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="c2049-138">Po kilku sekundach miniatura nowego obrazu powinna pojawić się na stronie.</span><span class="sxs-lookup"><span data-stu-id="c2049-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="c2049-139">Umieść kursor nad obrazem, aby zobaczyć opis wygenerowany przez funkcję przetwarzania obrazów.</span><span class="sxs-lookup"><span data-stu-id="c2049-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="c2049-140">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="c2049-140">Summary</span></span>

<span data-ttu-id="c2049-141">W tym module przedstawiono, jak automatycznie generować podpis dla każdego przekazanego obrazu przy użyciu interfejsu API przetwarzania obrazów w usługach Microsoft Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="c2049-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="c2049-142">Następnie dowiesz się, jak dodać opcję uwierzytelniania do aplikacji przy użyciu uwierzytelniania usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="c2049-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>
