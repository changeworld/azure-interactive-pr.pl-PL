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
Aplikacja, którą tworzysz, to galeria zdjęć. Używa ona języka JavaScript po stronie klienta do wywoływania interfejsów API umożliwiających przekazywanie i wyświetlanie obrazów. W tym module utworzysz interfejs API, który przy użyciu funkcji bezserwerowej generuje ograniczony czasowo adres URL umożliwiający przekazanie obrazu. Aplikacja internetowa używa wygenerowanego adresu URL do przekazania obrazu do usługi Blob Storage przy użyciu [interfejsu API REST usługi Blob Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

## <a name="create-a-blob-storage-container-for-images"></a>Tworzenie kontenera usługi Blob Storage do przechowywania obrazów

Aplikacja wymaga oddzielnego kontenera magazynu na potrzeby przekazywania i hostowania obrazów.

1. Upewnij się, że jesteś zalogowanym użytkownikiem usługi Cloud Shell (w powłoce bash). Jeśli nie, wybierz pozycję **Wejdź w tryb koncentracji uwagi**, aby otworzyć okno usługi Cloud Shell.

1.  Korzystając ze swojego konta magazynu, utwórz nowy kontener o nazwie **images** z publicznym dostępem do wszystkich obiektów blob.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

Azure Functions to usługa umożliwiająca uruchamianie funkcji bezserwerowych. Funkcja bezserwerowa może być wyzwalana (wywoływana) przez zdarzenia, takie jak żądanie HTTP lub utworzenie obiektu blob w kontenerze magazynu.

Aplikacja funkcji platformy Azure to kontener przeznaczony na jedną lub większą liczbę funkcji bezserwerowych.

1. Utwórz nową aplikację funkcji platformy Azure o unikatowej nazwie w utworzonej wcześniej grupie zasobów o nazwie **first-serverless-app**. Aplikacje funkcji wymagają konta magazynu. W tym samouczku użyjesz istniejącego konta magazynu.

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a>Tworzenie funkcji bezserwerowej wyzwalanej przez protokół HTTP

Aplikacja internetowa do obsługi galerii zdjęć wysyła żądanie HTTP do funkcji bezserwerowej w celu wygenerowania ograniczonego czasowo adresu URL na potrzeby bezpiecznego przekazania obrazu do usługi Blob Storage. Funkcja jest wyzwalana przez żądanie HTTP i używa zestawu SDK usługi Azure Storage w celu wygenerowania i zwrócenia bezpiecznego adresu URL.

1. Po utworzeniu aplikacji funkcji wyszukaj ją w witrynie Azure Portal, korzystając z pola wyszukiwania, i kliknij jej nazwę, aby ją otworzyć.

    ![Otwieranie aplikacji funkcji](media/functions-first-serverless-web-app/2-search-function-app.png)

1. W obszarze nawigacji po lewej stronie okna aplikacji funkcji umieść kursor nad opcją **Funkcje** i kliknij znak **+**, aby rozpocząć tworzenie nowej funkcji bezserwerowej.

    ![Tworzenie nowej funkcji](media/functions-first-serverless-web-app/2-new-function.png)

1. Kliknij pozycję **Funkcja niestandardowa**, aby zobaczyć listę szablonów funkcji.

1. Znajdź szablon **HttpTrigger** i kliknij odpowiedni język (C# lub JavaScript).

1. Użyj następujących wartości, aby utworzyć funkcję, która generuje adres URL umożliwiający przekazanie obiektu blob.

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | ---|
    | **Język** | C# lub JavaScript | Wybierz język, którego chcesz użyć. |
    | **Nazwa funkcji** | GetUploadUrl | Wpisz tę nazwę dokładnie tak, jak pokazano, aby aplikacja mogła odnaleźć funkcję. |
    | **Poziom autoryzacji** | Anonimowe | Zezwalaj na publiczny dostęp do funkcji. |

    ![Wprowadzanie ustawień nowej funkcji wyzwalanej przez protokół HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. Kliknij przycisk **Utwórz**, aby utworzyć funkcję.

1. **C#** 

    1. Gdy pojawi się kod źródłowy funkcji, zastąp całą zawartość pliku **run.csx** zawartością pliku [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).

1. **JavaScript** 

    1. (JavaScript) Ta funkcja wymaga pakietu `azure-storage` narzędzia npm w celu wygenerowania tokenu sygnatury dostępu współdzielonego (SAS) wymaganego do utworzenia bezpiecznego adresu URL. Aby zainstalować pakiet npm, kliknij nazwę aplikacji funkcji w lewym obszarze nawigacji i kliknij pozycję **Funkcje platformy**.

    1. (JavaScript) Kliknij pozycję **Konsola**, aby wyświetlić okno konsoli.

        ![Otwieranie okna konsoli](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) Upewnij się, bieżący katalog to **d:\home\site\wwwroot**, uruchamiając polecenie `cd d:\home\site\wwwroot`.

    1. (JavaScript) Uruchom polecenie `npm init -y`, aby utworzyć pusty plik **package.json**.

    1. (JavaScript) Uruchom polecenie `npm install --save azure-storage` w konsoli, aby zainstalować pakiet i zapisać go w pliku **package.json**. Wykonanie tej operacji może potrwać minutę lub dwie.

    1. (JavaScript) Kliknij nazwę funkcji (**GetUploadUrl**) w obszarze nawigacji po lewej stronie, aby wyświetlić funkcję, a następnie zastąp całą zawartość pliku **index.js** zawartością pliku [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).
    
        ![Plik index.js po aktualizacji](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.

1. Kliknij pozycję **Zapisz**. Na panelu dzienników sprawdź, czy funkcja została pomyślnie skompilowana.

Funkcja generuje tak zwany adresu URL sygnatury dostępu współdzielonego (SAS), który jest używany do przekazania pliku do usługi Blob Storage. Adres URL sygnatury dostępu współdzielonego jest ważny przez krótki okres i umożliwia przekazanie tylko jednego pliku. Zapoznaj się z dokumentacją usługi Blob Storage, aby uzyskać więcej informacji na temat [korzystania z sygnatur dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>Dodawanie zmiennej środowiskowej dla parametrów połączenia magazynu

Utworzona właśnie funkcja wymaga parametrów połączenia dla konta magazynu, aby móc wygenerować adres URL sygnatury dostępu współdzielonego. Zamiast zapisywać parametry połączenia w kodzie w treści funkcji, można je zapisać w postaci ustawień aplikacji. Ustawienia aplikacji są dostępne jako zmienne środowiskowe dla wszystkich funkcji w aplikacji funkcji.

1. W usłudze Cloud Shell wyślij zapytanie o parametry połączenia konta magazynu i zapisz je w zmiennej powłoki bash o nazwie **STORAGE_CONNECTION_STRING**.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    Upewnij się, że zmienna została pomyślnie ustawiona.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. Utwórz nowe ustawienie aplikacji o nazwie **AZURE_STORAGE_CONNECTION_STRING** przy użyciu wartości zapisanej w poprzednim kroku.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    Upewnij się, że dane wyjściowe polecenia zawierają nowe ustawienia aplikacji z poprawnymi wartościami.


## <a name="test-the-serverless-function"></a>Testowanie funkcji bezserwerowej

Oprócz możliwości tworzenia i edytowania funkcji witryna Azure Portal udostępnia również wbudowane narzędzie do ich testowania.

1. Aby przetestować funkcję bezserwerową HTTP, kliknij kartę **Test** po prawej stronie okna kodu w celu rozwinięcia panelu testu.

1. Zmień **metodę Http** na **GET**.

1. W obszarze **Zapytanie** kliknij pozycję *Dodaj parametr** i dodaj następujący parametr:

    | nazwa      |  wartość   | 
    | --- | --- |
    | filename | image1.jpg |

1. Kliknij przycisk **Uruchom** w panelu testu, aby wysłać żądanie HTTP do funkcji.

1. Funkcja zwraca w danych wyjściowych adres URL przekazywania. Wykonanie funkcji pojawia się na panelu dzienników.

    ![Dziennik z informacjami o pomyślnym przebiegu funkcji](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>Konfigurowanie mechanizmu CORS w aplikacji funkcji

Ponieważ fronton aplikacji jest hostowany w usłudze Blob Storage, ma inną nazwę domeny niż aplikacja funkcji platformy Azure. Aby kod JavaScript po stronie klienta mógł pomyślnie wywołać utworzoną właśnie funkcję, aplikacja funkcji musi zostać skonfigurowana na potrzeby współużytkowania zasobów między źródłami (CORS).

1. Na pasku nawigacyjnym po lewej stronie okna aplikacji funkcji kliknij nazwę aplikacji funkcji.

1. Kliknij pozycję **Funkcje platformy**, aby wyświetlić listę funkcji zaawansowanych.

1. W obszarze **API** kliknij pozycję **CORS**.

    ![Wybieranie mechanizmu CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. Dodaj dozwolone źródło dla adresu URL aplikacji z poprzedniego modułu z pominięciem końcowego znaku **/** (na przykład: `https://firstserverlessweb.z4.web.core.windows.net`).

    ![Ustawienia mechanizmu CORS z widocznym dodanym adresem URL bezserwerowej aplikacji internetowej](media/functions-first-serverless-web-app/2-add-cors.png)

1. Kliknij pozycję **Zapisz**.

1. C#

   1. (C#) Przejdź z powrotem do funkcji `GetUploadUrl`, a następnie wybierz kartę **Integracja**.

   1. (C#) W obszarze Wybrane metody HTTP wybierz pozycję **OPTIONS**.

      Powinny zostać wybrane wszystkie metody: **GET**, **POST** i **OPTIONS**. Mechanizm CORS korzysta z metody OPTIONS, która nie jest domyślnie wybrana dla funkcji w języku C#.  

   1. (C#) Kliknij pozycję **Zapisz**.

1. Będąc cały czas w portalu, przejdź do aplikacji funkcji, wybierz kartę **Przegląd**, a następnie kliknij pozycję **Uruchom ponownie**, aby upewnić się, że zmiany mechanizmu CORS zostaną uwzględnione.

## <a name="configure-cors-in-the-storage-account"></a>Konfigurowanie mechanizmu CORS w ramach konta magazynu

Ponieważ aplikacja wykonuje również wywołania w języku JavaScript po stronie klienta do usługi Blob Storage w celu przekazania plików, należy także skonfigurować konto magazynu do obsługi mechanizmu CORS.

1. Uruchom następujące polecenie, aby zezwolić na przekazywanie plików do konta magazynu ze wszystkich źródeł.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>Modyfikowanie aplikacji internetowej na potrzeby przekazywania obrazów

Aplikacja internetowa pobiera ustawienia z pliku o nazwie **settings.js**. W poniższych krokach utworzysz plik za pomocą usługi Cloud Shell, a następnie ustawisz parametr `window.apiBaseUrl` na adres URL aplikacji funkcji, a parametr `window.blobBaseUrl` na adres URL punktu końcowego usługi Azure Blob Storage.

1. W usłudze Cloud Shell upewnij się, że bieżący katalog to folder **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Wyślij zapytanie o adres URL aplikacji funkcji i zapisz go w zmiennej powłoki bash o nazwie **FUNCTION_APP_URL**.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    Upewnij się, że zmienna jest prawidłowo ustawiona.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. Aby ustawić podstawowy identyfikator URI wywołań interfejsu API do aplikacji funkcji, utwórz plik **settings.js** i dodaj do niego adres URL aplikacji funkcji, jak pokazano poniżej.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    Tę zmianę możesz wprowadzić, uruchamiając następujące polecenie lub korzystając z wierszowego edytora poleceń, na przykład VIM.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    Upewnij się, że plik został pomyślnie zapisany.

    ```azurecli
    cat settings.js
    ```

1. Wyślij zapytanie o podstawowy adres URL usługi Blob Storage i zapisz go w zmiennej powłoki bash, o nazwie **BLOB_BASE_URL**.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    Upewnij się, że zmienna jest prawidłowo ustawiona.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. Aby ustawić podstawowy identyfikator URI wywołań interfejsu API do aplikacji funkcji, dołącz adres URL magazynu do pliku **settings.js** w sposób pokazany w następującym wierszu kodu.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    Tę zmianę możesz wprowadzić, uruchamiając następujące polecenie lub korzystając z wierszowego edytora poleceń, na przykład VIM.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    Upewnij się, że plik został pomyślnie zapisany oraz że zawiera teraz 2 wiersze.

    ```azurecli
    cat settings.js
    ```

1. Przekaż plik do usługi Blob Storage.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>Testowanie aplikacji internetowej

Na tym etapie za pomocą aplikacji galerii można przekazać obraz do usługi Blob Storage, ale nie można jeszcze wyświetlać obrazów. Aplikacja podejmie próbę wywołania funkcji `GetImages`, która jeszcze nie istnieje, ponieważ utworzysz ją dopiero w jednym z następnych modułów. Wywołanie nie powiedzie się i zostanie wyświetlona strona internetowa z komunikatem „Analizowanie...”, ale wybrany obraz zostanie pomyślnie przekazany.

O pomyślnym przekazaniu obrazu możesz się przekonać, sprawdzając zawartość kontenera **images** w witrynie Azure Portal.

1. W oknie przeglądarki przejdź do aplikacji. Wybierz plik obrazu i przekaż go. Przekazywanie zostaje zakończone, ale ponieważ nie dodaliśmy jeszcze możliwości wyświetlania obrazów, aplikacja nie pokazuje przekazanego zdjęcia. Strona internetowa wydaje się być zawieszona z komunikatem „Analizowanie obrazu...”. Naprawisz to później.

1. W usłudze Cloud Shell upewnij się, że obraz został przekazany do kontenera **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Przed przejściem do następnego samouczka usuń wszystkie pliki z kontenera **images**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>Podsumowanie

W tym module utworzono aplikację funkcji platformy Azure i przedstawiono sposób użycia funkcji bezserwerowej w celu umożliwienia aplikacji internetowej przekazania obrazów do usługi Blob Storage. Dalej dowiesz się, jak utworzyć miniatury przekazanych obrazów przy użyciu funkcji bezserwerowej wyzwalanej za pomocą obiektu Blob.
