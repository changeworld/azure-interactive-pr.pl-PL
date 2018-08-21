Usługa Azure Blob Storage to zapewniająca niskie koszty, wysoce skalowalna usługa, której można użyć do hostowania plików statycznych. W tym samouczku użyjesz tej usługi, aby obsługiwać zawartość statyczną (np. pliki HTML, JavaScript, CSS) na potrzeby tworzonej aplikacji internetowej.

## <a name="create-a-storage-account"></a>Tworzenie konta usługi Storage

Konto usługi Storage to zasób platformy Azure, który umożliwia przechowywanie tabel, kolejek, plików, obiektów blob (obiektów) oraz dysków maszyn wirtualnych.

1. Zaloguj się w usłudze Cloud Shell (Bash), wybierając przycisk **Wejdź w tryb koncentracji uwagi**. Ten przycisk znajduje się w prawym górnym rogu lub u dołu strony, w zależności od tego, jak szerokie jest okno przeglądarki. Tryb koncentracji uwagi dokuje okno usługi Cloud Shell po prawej stronie od okna przeglądarki, dzięki czemu można łatwo wykonywać polecenia przedstawiane w tym samouczku.

1. Na platformie Azure grupa zasobów to kontener przechowujący zasoby związane z platformą Azure, aby ułatwić zarządzanie. Utwórz nową grupę zasobów o nazwie **first-serverless-app**.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. Zawartość statyczna (pliki HTML, CSS i JavaScript) na potrzeby tego samouczka jest hostowana w usłudze Blob Storage. Usługa Blob Storage wymaga konta usługi Storage. Utwórz konto usługi Storage (ogólnego przeznaczenia w wersji 2) w grupie zasobów. Zastąp `<storage account name>` unikatową nazwą.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. Korzystając z paska wyszukiwania w górnej części witryny [Azure Portal](https://portal.azure.com), znajdź właśnie utworzone konto magazynu i otwórz je.

1. Na lewym pasku nawigacyjnym wybierz opcję **Statyczna witryna internetowa (wersja zapoznawcza)**, aby skonfigurować kontener na potrzeby hostowania statycznej witryny internetowej.
    - Wybierz opcję **Włączone**, aby włączyć statyczną witrynę internetową.
    - Wprowadź *index.html* jako nazwę dokumentu indeksu. Pole zawiera już szary tekst *index.html*, ale jest to wyłącznie tekst przykładowy; nadal należy wprowadzić wartość w polu.
    - Kliknij pozycję **Zapisz**.
    
    ![Wprowadzanie ustawień statycznej witryny internetowej](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Zapisz **podstawowy punkt końcowy** w miejscu, z którego możesz wygodnie go skopiować podczas pracy z samouczkiem. Jest to adres URL aplikacji internetowej.

## <a name="upload-the-web-application"></a>Przekazywanie aplikacji internetowej

1. Pliki źródłowe aplikacji, którą tworzysz w tym samouczku, znajdują się w [repozytorium GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application). Upewnij się, że znajdujesz się w katalogu głównym w usłudze Cloud Shell, a następnie sklonuj to repozytorium.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    Repozytorium jest klonowane do lokalizacji `/home/<username>/functions-first-serverless-web-application`.

1. Aplikacja internetowa po stronie klienta znajduje się w folderze **www** i jest kompilowana przy użyciu struktury Vue.js JavaScript. Przejdź do folderu i uruchom polecenia npm, aby zainstalować zależności aplikacji oraz skompilować aplikację. Wykonanie ostatniego z tych poleceń może potrwać kilka minut.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    Aplikacja jest generowana w folderze **dist**.

1. Zmień bieżący katalog na folder **dist** i przekaż aplikację do kontenera obiektów blob **$web**.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Otwórz adres URL podstawowego punktu końcowego statycznych witryn internetowych usługi Storage w przeglądarce internetowej, aby wyświetlić aplikację.

    ![Strona główna pierwszej bezserwerowej aplikacji internetowej](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Podsumowanie

W tym module utworzono grupę zasobów o nazwie **first-serverless-app** zawierającą konto usługi Storage. Kontener obiektów blob o nazwie **$web** na koncie usługi Storage przechowuje zawartość statyczną aplikacji internetowej oraz udostępnia zawartość publicznie. Dalej dowiesz się, jak używać funkcji bezserwerowej, aby przekazywać obrazy do magazynu obiektów blob z tej aplikacji internetowej.
