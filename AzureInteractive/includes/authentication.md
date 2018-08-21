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
Uwierzytelnianie usługi Azure App Service zapewnia kompleksową obsługę uwierzytelniania w aplikacji funkcji na platformie Azure. Usługa integruje się bezproblemowo z serwisami Facebook i Twitter, kontem Microsoft, Google oraz usługą Azure Active Directory. Musisz dodać uwierzytelnianie usługi App Service, aby ochronić interfejsy API zaplecza aplikacji internetowej.

## <a name="enable-app-service-authentication"></a>Włączanie uwierzytelniania usługi App Service

1. Otwórz aplikację funkcji w witrynie Azure Portal.

1. W obszarze **Funkcje platformy** wybierz opcję **Uwierzytelnianie/autoryzacja**.

    ![Wybieranie pozycji Uwierzytelnianie/autoryzacja](media/functions-first-serverless-web-app/6-authorization.jpg)

1. Wybierz poniższe wartości:
    
    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | ---|
    | **Uwierzytelnianie usługi App Service** | Włączone | Włącz uwierzytelnianie. |
    | **Akcja do wykonania w przypadku nieuwierzytelnionego żądania** | Zaloguj się za pomocą usługi Azure Active Directory | Wybierz skonfigurowaną metodę uwierzytelniania (poniżej). |
    | **Dostawcy uwierzytelniania** | Zobacz poniżej | Zobacz poniżej |
    | **Magazyn tokenów** | Włączone | Zezwalaj usłudze App Service na przechowywanie tokenów i zarządzanie nimi. |
    | **Dozwolone adresy URL zewnętrznego przekierowania** | Adres URL aplikacji, na przykład: https://firstserverlessweb.z4.web.core.windows.net/ | Adresy URL, do których usługa App Service może wykonać przekierowanie, jeżeli użytkownik jest uwierzytelniony. |

1. Wybierz usługę **Azure Active Directory**, aby wyświetlić **ustawienia usługi Azure Active Directory**.

    1. Wybierz opcję **Ekspresowy** jako **Tryb zarządzania** i podaj następujące informacje.
    
        | Ustawienie      |  Sugerowana wartość   | Opis                                        |
        | --- | --- | ---|
        | **Tryb zarządzania** | Ekspresowy, tworzenie nowej aplikacji usługi AD | Automatycznie ustaw nazwę główną usługi oraz uwierzytelnianie usługi Azure Active Directory. |
        | **Tworzenie aplikacji** | my-serverless-webapp | Wprowadź unikatową nazwę aplikacji. |
    
    1. Kliknij przycisk **OK**, aby zapisać ustawienia usługi Azure Active Directory.

    ![Uwierzytelnianie/autoryzacja oraz ustawienia usługi Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. Kliknij pozycję **Zapisz**.


## <a name="modify-the-web-app-to-enable-authentication"></a>Modyfikowanie aplikacji internetowej w celu włączenia uwierzytelniania

1. W usłudze Cloud Shell upewnij się, że bieżący katalog to folder **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Aby włączyć uwierzytelnianie w aplikacji funkcji, dołącz następujący wiersz kodu do pliku **settings.js**.

    `window.authEnabled = true`

    Wprowadź zmianę i wyświetl wyniki, korzystając z następujących poleceń lub używając wierszowego edytora poleceń, np. VIM.

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    Upewnij się, że w pliku wprowadzono zmiany.

    ```azurecli
    cat settings.js
    ```

1. Przekaż plik do usługi Blob Storage.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>Testowanie aplikacji

1. Otwórz aplikację w przeglądarce. Kliknij przycisk **Zaloguj** i zaloguj się.

1. Wybierz plik obrazu i przekaż go.

    ![Strona logowania](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>Podsumowanie

W tym module przedstawiono sposób dodawania uwierzytelniania do aplikacji przy użyciu uwierzytelniania usługi App Service.
