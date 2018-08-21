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
ms.openlocfilehash: d1f9a07ce3d3b096b498e48b5c4f68c3454b2b37
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079492"
---
<span data-ttu-id="d2872-103">Uwierzytelnianie usługi Azure App Service zapewnia kompleksową obsługę uwierzytelniania w aplikacji funkcji na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="d2872-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="d2872-104">Usługa integruje się bezproblemowo z serwisami Facebook i Twitter, kontem Microsoft, Google oraz usługą Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d2872-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="d2872-105">Musisz dodać uwierzytelnianie usługi App Service, aby ochronić interfejsy API zaplecza aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="d2872-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="d2872-106">Włączanie uwierzytelniania usługi App Service</span><span class="sxs-lookup"><span data-stu-id="d2872-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="d2872-107">Otwórz aplikację funkcji w witrynie Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="d2872-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="d2872-108">W obszarze **Funkcje platformy** wybierz opcję **Uwierzytelnianie/autoryzacja**.</span><span class="sxs-lookup"><span data-stu-id="d2872-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Wybieranie pozycji Uwierzytelnianie/autoryzacja](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="d2872-110">Wybierz poniższe wartości:</span><span class="sxs-lookup"><span data-stu-id="d2872-110">Select the following values:</span></span>
    
    | <span data-ttu-id="d2872-111">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="d2872-111">Setting</span></span>      |  <span data-ttu-id="d2872-112">Sugerowana wartość</span><span class="sxs-lookup"><span data-stu-id="d2872-112">Suggested value</span></span>   | <span data-ttu-id="d2872-113">Opis</span><span class="sxs-lookup"><span data-stu-id="d2872-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="d2872-114">**Uwierzytelnianie usługi App Service**</span><span class="sxs-lookup"><span data-stu-id="d2872-114">**App Service Authentication**</span></span> | <span data-ttu-id="d2872-115">Włączone</span><span class="sxs-lookup"><span data-stu-id="d2872-115">On</span></span> | <span data-ttu-id="d2872-116">Włącz uwierzytelnianie.</span><span class="sxs-lookup"><span data-stu-id="d2872-116">Enable authentication.</span></span> |
    | <span data-ttu-id="d2872-117">**Akcja do wykonania w przypadku nieuwierzytelnionego żądania**</span><span class="sxs-lookup"><span data-stu-id="d2872-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="d2872-118">Zaloguj się za pomocą usługi Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="d2872-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="d2872-119">Wybierz skonfigurowaną metodę uwierzytelniania (poniżej).</span><span class="sxs-lookup"><span data-stu-id="d2872-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="d2872-120">**Dostawcy uwierzytelniania**</span><span class="sxs-lookup"><span data-stu-id="d2872-120">**Authentication Providers**</span></span> | <span data-ttu-id="d2872-121">Zobacz poniżej</span><span class="sxs-lookup"><span data-stu-id="d2872-121">See below</span></span> | <span data-ttu-id="d2872-122">Zobacz poniżej</span><span class="sxs-lookup"><span data-stu-id="d2872-122">See below</span></span> |
    | <span data-ttu-id="d2872-123">**Magazyn tokenów**</span><span class="sxs-lookup"><span data-stu-id="d2872-123">**Token store**</span></span> | <span data-ttu-id="d2872-124">Włączone</span><span class="sxs-lookup"><span data-stu-id="d2872-124">On</span></span> | <span data-ttu-id="d2872-125">Zezwalaj usłudze App Service na przechowywanie tokenów i zarządzanie nimi.</span><span class="sxs-lookup"><span data-stu-id="d2872-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="d2872-126">**Dozwolone adresy URL zewnętrznego przekierowania**</span><span class="sxs-lookup"><span data-stu-id="d2872-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="d2872-127">Adres URL aplikacji, na przykład: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="d2872-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="d2872-128">Adresy URL, do których usługa App Service może wykonać przekierowanie, jeżeli użytkownik jest uwierzytelniony.</span><span class="sxs-lookup"><span data-stu-id="d2872-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="d2872-129">Wybierz usługę **Azure Active Directory**, aby wyświetlić **ustawienia usługi Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="d2872-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="d2872-130">Wybierz opcję **Ekspresowy** jako **Tryb zarządzania** i podaj następujące informacje.</span><span class="sxs-lookup"><span data-stu-id="d2872-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="d2872-131">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="d2872-131">Setting</span></span>      |  <span data-ttu-id="d2872-132">Sugerowana wartość</span><span class="sxs-lookup"><span data-stu-id="d2872-132">Suggested value</span></span>   | <span data-ttu-id="d2872-133">Opis</span><span class="sxs-lookup"><span data-stu-id="d2872-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="d2872-134">**Tryb zarządzania**</span><span class="sxs-lookup"><span data-stu-id="d2872-134">**Management mode**</span></span> | <span data-ttu-id="d2872-135">Ekspresowy, tworzenie nowej aplikacji usługi AD</span><span class="sxs-lookup"><span data-stu-id="d2872-135">Express, Create new AD app</span></span> | <span data-ttu-id="d2872-136">Automatycznie ustaw nazwę główną usługi oraz uwierzytelnianie usługi Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d2872-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="d2872-137">**Tworzenie aplikacji**</span><span class="sxs-lookup"><span data-stu-id="d2872-137">**Create app**</span></span> | <span data-ttu-id="d2872-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="d2872-138">my-serverless-webapp</span></span> | <span data-ttu-id="d2872-139">Wprowadź unikatową nazwę aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d2872-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="d2872-140">Kliknij przycisk **OK**, aby zapisać ustawienia usługi Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="d2872-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Uwierzytelnianie/autoryzacja oraz ustawienia usługi Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="d2872-142">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="d2872-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="d2872-143">Modyfikowanie aplikacji internetowej w celu włączenia uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="d2872-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="d2872-144">W usłudze Cloud Shell upewnij się, że bieżący katalog to folder **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="d2872-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="d2872-145">Aby włączyć uwierzytelnianie w aplikacji funkcji, dołącz następujący wiersz kodu do pliku **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="d2872-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="d2872-146">Wprowadź zmianę i wyświetl wyniki, korzystając z następujących poleceń lub używając wierszowego edytora poleceń, np. VIM.</span><span class="sxs-lookup"><span data-stu-id="d2872-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="d2872-147">Upewnij się, że w pliku wprowadzono zmiany.</span><span class="sxs-lookup"><span data-stu-id="d2872-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="d2872-148">Przekaż plik do usługi Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="d2872-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="d2872-149">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="d2872-149">Test the application</span></span>

1. <span data-ttu-id="d2872-150">Otwórz aplikację w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="d2872-150">Open the application in a browser.</span></span> <span data-ttu-id="d2872-151">Kliknij przycisk **Zaloguj** i zaloguj się.</span><span class="sxs-lookup"><span data-stu-id="d2872-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="d2872-152">Wybierz plik obrazu i przekaż go.</span><span class="sxs-lookup"><span data-stu-id="d2872-152">Select an image file and upload it.</span></span>

    ![Strona logowania](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="d2872-154">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="d2872-154">Summary</span></span>

<span data-ttu-id="d2872-155">W tym module przedstawiono sposób dodawania uwierzytelniania do aplikacji przy użyciu uwierzytelniania usługi App Service.</span><span class="sxs-lookup"><span data-stu-id="d2872-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
