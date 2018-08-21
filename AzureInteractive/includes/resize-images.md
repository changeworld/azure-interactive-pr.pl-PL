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
ms.openlocfilehash: 88b0ac838dfa8e097a30cc6cef591377e4a95ad8
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079360"
---
W poprzednim module przedstawiono, jak funkcja bezserwerowa może ułatwić bezpieczne przekazywanie obrazów do magazynu obiektów blob z aplikacji internetowej. W tym module utworzysz kolejną funkcję bezserwerową, aby obserwować przekazywane obrazy i tworzyć dla nich miniatury.

## <a name="create-a-blob-storage-container-for-thumbnails"></a>Tworzenie kontenera usługi Blob Storage do przechowywania miniatur

Obrazy o pełnym rozmiarze są przechowywane w kontenerze o nazwie **images**. Potrzebujesz kolejnego kontenera do przechowywania miniatur tych obrazów.

1. Upewnij się, że jesteś zalogowanym użytkownikiem usługi Cloud Shell (w powłoce bash).  Jeśli nie, wybierz pozycję **Wejdź w tryb koncentracji uwagi**, aby otworzyć okno usługi Cloud Shell. 

1. Korzystając ze swojego konta magazynu, utwórz nowy kontener o nazwie **thumbnails** z publicznym dostępem do wszystkich obiektów blob.

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>Tworzenie bezserwerowej funkcji wyzwalanej przez obiekty blob

Wyzwalacz definiuje sposób wywoływania funkcji. Funkcja, którą utworzysz następnie, używa wyzwalacza obiektów blob: funkcja jest automatycznie wywoływana, gdy obiekt blob (plik obrazu) jest przekazywany do kontenera **images**. Funkcja musi mieć jeden wyzwalacz. Wyzwalacze mają skojarzone dane będące zwykle ładunkiem, który wyzwolił funkcję.

Powiązania definiują sposób odczytu i zapisu danych przez funkcję na platformie Azure lub w usługach innych firm. Ta funkcja tworzy wersję miniatury obrazu, który ją wyzwolił, i zapisuje miniaturę w kontenerze *thumbnails*.

1. Otwórz aplikację funkcji w witrynie Azure Portal.

1. W obszarze nawigacji po lewej stronie okna aplikacji funkcji umieść kursor nad opcją **Funkcje** i kliknij znak **+**, aby rozpocząć tworzenie nowej funkcji bezserwerowej. Jeżeli zostanie wyświetlona strona przewodnika Szybki start, kliknij opcję **Funkcja niestandardowa**, aby zobaczyć listę szablonów funkcji.

1. Znajdź szablon **BlobTrigger** i wybierz go.

1. Użyj tych wartości, aby utworzyć funkcję, która tworzy miniatury podczas przekazywania obrazów.

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | ---|
    | **Język** | C# lub JavaScript | Wybierz preferowany język. |
    | **Nazwa funkcji** | ResizeImage | Wpisz tę nazwę dokładnie tak, jak pokazano, aby aplikacja mogła odnaleźć funkcję. |
    | **Ścieżka** | images/{nazwa} | Wykonaj funkcję, gdy plik pojawi się w kontenerze **images**. |
    | **Informacje o koncie magazynu** | AZURE_STORAGE_CONNECTION_STRING | Użyj zmiennej środowiskowej utworzonej poprzednio z parametrami połączenia. |

    ![Wprowadzanie ustawień dla nowej funkcji](media/functions-first-serverless-web-app/3-new-function.png)

1. Kliknij przycisk **Utwórz**, aby utworzyć funkcję.

1. Po utworzeniu funkcji kliknij przycisk **Integruj**, aby wyświetlić jej wyzwalacz oraz powiązania wejściowe i wyjściowe.

1. Kliknij przycisk **Nowe dane wyjściowe**, aby utworzyć nowe powiązanie wyjściowe.

    ![Wybieranie opcji Nowe dane wyjściowe na karcie Integracja](media/functions-first-serverless-web-app/3-new-output.jpg)

1. Wybierz pozycję **Azure Blob Storage**, a następnie kliknij przycisk **Wybierz**. Może być konieczne przewinięcie w dół w celu wyświetlenia przycisku **Wybierz**.

    ![Wybieranie pozycji Azure Blob Storage](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. Wprowadź następujące wartości.

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | ---|
    | **Nazwa parametru obiektu blob** | miniatura | Funkcja używa parametru z tą nazwą do zapisania miniatury. |
    | **Użyj wartości zwracanej funkcji** | Nie |  |
    | **Ścieżka** | thumbnails/{nazwa} | Miniatury są zapisywane w kontenerze o nazwie **thumbnails**. |
    | **Połączenie konta magazynu** | AZURE_STORAGE_CONNECTION_STRING | Użyj zmiennej środowiskowej utworzonej poprzednio z parametrami połączenia. |

    ![Wprowadzanie ustawień powiązania wyjściowego obiektu blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript**

    1. (JavaScript) Kliknij opcję **Edytor zaawansowany** w prawym górnym rogu okna, aby wyświetlić kod JSON reprezentujący powiązania.

    1. (JavaScript) W powiązaniu `blobTrigger` dodaj właściwość o nazwie `dataType` z wartością `binary`. Umożliwi to skonfigurowanie powiązania w celu przekazania zawartości obiektu blob do funkcji w postaci danych binarnych.

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

1. Kliknij przycisk **Zapisz**, aby utworzyć nowe powiązanie.

1. **C#**

    1. (C#) Wybierz nazwę funkcji **ResizeImage** na lewym pasku nawigacyjnym, aby otworzyć kod źródłowy funkcji.

    1. (C#) Funkcja wymaga pakietu NuGet o nazwie **ImageResizer** w celu generowania miniatur. Pakiety NuGet są dodawane do funkcji C# przy użyciu pliku **project.json**. Aby utworzyć plik, kliknij przycisk **Wyświetl pliki** po prawej stronie w celu wyświetlenia plików tworzących funkcję.
    
    1. (C#) Kliknij przycisk **Dodaj**, aby dodać nowy plik o nazwie **project.json**.
    
    1. (C#) Skopiuj zawartość pliku [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) do nowo utworzonego pliku. Zapisz plik. Pakiety zostaną automatycznie przywrócone w przypadku zaktualizowania pliku.
    
        ![Plik project.json z pakietem ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) Wybierz plik **run.csx** w obszarze **Wyświetl pliki** i zastąp jego zawartość zawartością pliku [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).

1. **JavaScript** 

    1. (JavaScript) Ta funkcja wymaga pakietu `jimp` z programu npm w celu zmiany rozmiaru zdjęcia. Aby zainstalować pakiet npm, kliknij nazwę aplikacji funkcji w lewym obszarze nawigacji i kliknij pozycję **Funkcje platformy**.

    1. (JavaScript) Kliknij pozycję **Konsola**, aby wyświetlić okno konsoli.

    1. (JavaScript) Uruchom polecenie `npm install jimp` w konsoli. Wykonanie tej operacji może potrwać minutę lub dwie.

    1. (JavaScript) Kliknij nazwę funkcji **ResizeImage** na lewym pasku nawigacyjnym, aby wyświetlić funkcję, a następnie zastąp całą zawartość pliku **index.js** zawartością pliku [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).

1. Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.

1. Kliknij pozycję **Zapisz**. Sprawdź panel dzienników, aby upewnić się, że funkcja została prawidłowo zapisana oraz że nie wystąpiły błędy.


## <a name="test-the-serverless-function"></a>Testowanie funkcji bezserwerowej

1. Otwórz aplikację w przeglądarce. Wybierz plik obrazu i przekaż go. Przekazywanie zostaje zakończone, ale ponieważ nie dodaliśmy jeszcze możliwości wyświetlania obrazów, aplikacja nie pokazuje przekazanego zdjęcia.

1. W usłudze Cloud Shell upewnij się, że obraz został przekazany do kontenera **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Upewnij się, że miniatura została utworzona w kontenerze o nazwie **thumbnails**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. Uzyskaj adres URL miniatury.

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    Otwórz adres URL w przeglądarce i upewnij się, że miniatura została utworzona prawidłowo.

1. Przed przejściem do następnego samouczka należy usunąć wszystkie pliki z kontenerów **images** i **thumbnails**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>Podsumowanie

W tym module utworzono funkcję bezserwerową, aby tworzyć miniaturę za każdym razem, gdy obraz jest przekazywany do kontenera magazynu obiektów blob. Następnie dowiesz się, jak używać usługi Azure Cosmos DB do przechowywania i wyświetlania listy metadanych obrazu.
