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
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079468"
---
Azure Cosmos DB to dostępna w skali światowej, bezserwerowa, wielomodelowa baza danych firmy Microsoft. W tym module nauczysz się, jak używać usługi Azure Functions do przechowywania i pobierania metadanych obrazu w formie dokumentów JSON w usłudze Cosmos DB.

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Tworzenie konta, bazy danych i kolekcji usługi Cosmos DB

Konto usługi Cosmos DB to zasób platformy Azure, który zawiera bazy danych usługi Cosmos DB.

1. Upewnij się, że użytkownik jest nadal zalogowany w usłudze Cloud Shell.  Jeśli nie, wybierz pozycję **Wejdź w tryb koncentracji uwagi**, aby otworzyć okno usługi Cloud Shell. 

1. Utwórz konto usługi Cosmos DB o unikatowej nazwie w tej samej grupie zasobów co inne zasoby w tym samouczku.

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. Po utworzeniu konta usługi Cosmos DB utwórz nową bazę danych o nazwie **imagesdb** na koncie.

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. Po utworzeniu bazy danych utwórz nową kolekcję o nazwie **images** w bazie danych przy przepływności równej 400 jednostek żądań (RU).

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>Zapisywanie dokumentu do usługi Cosmos DB w momencie utworzenia miniatury

Powiązanie wyjściowe usługi Cosmos DB umożliwia tworzenie dokumentów w kolekcji usługi Cosmos DB z poziomu usługi Azure Functions. W poniższych krokach skonfigurujesz powiązanie wyjściowe usługi Cosmos DB w funkcji **ResizeImage** oraz zmodyfikujesz funkcję, aby zwracała dokument (obiekt) do zapisania.

1. Otwórz aplikację funkcji w witrynie Azure Portal.

1. W obszarze nawigacji po lewej stronie ekranu rozwiń funkcję **ResizeImage**, a następnie wybierz opcję **Integruj**.

1. W obszarze **Dane wyjściowe** kliknij przycisk **Nowe dane wyjściowe**.

1. Znajdź element **Azure Cosmos DB** i wybierz go. Następnie kliknij pozycję **Wybierz**.

    ![Wybieranie nowych danych wyjściowych](media/functions-first-serverless-web-app/4-new-output.jpg)

1. Wypełnij pola w pozycji **Dane wyjściowe usługi Azure Cosmos DB** przy użyciu następujących wartości.

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | ---|
    | **Nazwa parametru dokumentu** | Wybieranie opcji **Użyj wartości zwracanej funkcji** | Wartość pola tekstowego jest automatycznie ustawiana na **$return**. |
    | **Nazwa bazy danych** | imagesdb | Użyj nazwy utworzonej bazy danych. |
    | **Nazwa kolekcji** | images | Użyj nazwy utworzonej kolekcji. |

1. Obok pozycji **Połączenie konta usługi Azure Cosmos DB** kliknij opcję **nowe**. Wybierz wcześniej utworzone konto usługi Cosmos DB.

    ![Wprowadzanie ustawień dla powiązania wyjściowego usługi Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. Kliknij przycisk **Zapisz**, aby utworzyć powiązanie wyjściowe usługi Cosmos DB.

1. Kliknij nazwę funkcji **ResizeImage** po lewej stronie, aby otworzyć funkcję.

1. **C#**

    1. (C#) Zmień zwracany typ funkcji z metody **void** na **obiekt**.

    1. (C#) Na końcu funkcji dodaj następujący blok kodu, aby zwrócić dokument do zapisania:
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![Plik run.csx dla funkcji ResizeImages po wprowadzeniu modyfikacji](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript**

    1. (JavaScript) Zmień instrukcję `context.done()` w klauzuli `else`, aby zwrócić dokument do zapisania w usłudze Cosmos DB.

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

1. Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.

1. Kliknij pozycję **Zapisz**. Sprawdź panel dzienników, aby upewnić się, że funkcja została prawidłowo zapisana oraz że nie wystąpiły błędy.


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Tworzenie funkcji w celu wyświetlenia listy obrazów z usługi Cosmos DB

Aplikacja internetowa wymaga interfejsu API, aby pobierać metadane obrazów z usługi Cosmos DB. W poniższych krokach utworzysz funkcję z wyzwalaniem HTTP, która będzie używać powiązania wejściowego usługi Cosmos DB w celu wysyłania zapytań do kolekcji bazy danych.

1. W aplikacji funkcji umieść kursor nad pozycją **Funkcje** po lewej stronie i kliknij znak **+**, aby utworzyć nową funkcję.

1. Znajdź szablon **HttpTrigger** i wybierz go.

1. Użyj następujących wartości, aby utworzyć funkcję, która generuje adresy URL obrazów.

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | ---|
    | **Nazwa funkcji** | GetImages | Wpisz tę nazwę dokładnie tak, jak pokazano, aby aplikacja mogła odnaleźć funkcję. |
    | **Poziom autoryzacji** | Anonimowe | Zezwalaj na publiczny dostęp do funkcji. |

1. Kliknij pozycję **Utwórz**.

1. Po utworzeniu nowej funkcji kliknij opcję **Integruj** pod nazwą funkcji na lewym pasku nawigacyjnym.

1. Kliknij przycisk **Nowe dane wejściowe** i wybierz opcję **Azure Cosmos DB**. 

    ![Wybieranie nowych danych wejściowych](media/functions-first-serverless-web-app/4-new-input.jpg)

1. Kliknij pozycję **Wybierz**.

1. Wypełnij następujące wartości:

    | Ustawienie      |  Sugerowana wartość   | Opis                                        |
    | --- | --- | ---|
    | **Nazwa parametru dokumentu** | documents | Pasuje do nazwy parametru w funkcji. |
    | **Nazwa bazy danych** | imagesdb |  |
    | **Nazwa kolekcji** | images |  |
    | **Zapytanie SQL** | select * from c order by c._ts desc | Pobierz dokumenty, zaczynając od najnowszego dokumentu. |
    | **Połączenie konta usługi Azure Cosmos DB** | Wybieranie istniejących parametrów połączenia |  |

1. Kliknij przycisk **Zapisz**, aby utworzyć powiązanie wejściowe.

1. **C#**

    1. Kliknij nazwę funkcji, aby otworzyć okno kodu, a następnie zastąp całą zawartość pliku **run.csx** zawartością pliku [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).

1. **JavaScript**

    1. Kliknij nazwę funkcji, aby otworzyć okno kodu, a następnie zastąp całą zawartość pliku **index.js** zawartością pliku [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).

1. Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.

1. Kliknij pozycję **Zapisz**. Sprawdź panel dzienników, aby upewnić się, że funkcja została prawidłowo zapisana oraz że nie wystąpiły błędy.


## <a name="test-the-application"></a>Testowanie aplikacji

1. Otwórz aplikację w przeglądarce. Wybierz plik obrazu i przekaż go.

1. Po kilku sekundach miniatura nowego obrazu pojawi się na stronie.

1. W witrynie Azure Portal użyj pola wyszukiwania, aby wyszukać konto usługi Cosmos DB według nazwy. Kliknij konto, aby je otworzyć.

1. Kliknij przycisk **Eksplorator danych** po lewej stronie, aby przeglądać kolekcje i dokumenty.

1. W obszarze bazy danych **imagesdb** zaznacz kolekcję **images**.

1. Upewnij się, że dokument został utworzony dla przekazanego obrazu.

    ![Eksplorator danych przedstawiający dokument dla przekazanego obrazu](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>Podsumowanie

W tym module przedstawiono sposób tworzenia konta, bazy danych i kolekcji usługi Cosmos DB. Przedstawiono również sposób używania powiązań usługi Cosmos DB w celu zapisywania i pobierania metadanych obrazu w kolekcji usługi Cosmos DB. Następnie dowiesz się, jak automatycznie generować podpis dla każdego przekazanego obrazu przy użyciu usług Microsoft Cognitive Services.