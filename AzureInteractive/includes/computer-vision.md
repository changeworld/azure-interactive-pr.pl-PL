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
ms.openlocfilehash: 7e51d3cd0533b4fb64d7dfa783af55266d536f54
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079713"
---
Na tym etapie aplikacja jest funkcjonalną galerią, która umożliwia przekazywanie i wyświetlanie obrazów. W tym module dowiesz się, jak używać interfejsu API przetwarzania obrazów usług Microsoft Cognitive Services w celu wygenerowania podpisów dla przekazanych obrazów oraz jak zapisać podpisy w metadanych obrazów w usłudze Cosmos DB.

## <a name="create-a-computer-vision-account"></a>Tworzenie konta funkcji przetwarzania obrazów

Usługi Microsoft Cognitive Services to kolekcja usług dostępna dla deweloperów, która umożliwia im zwiększenie inteligencji aplikacji. Interfejs API przetwarzania obrazów to bezserwerowa usługa, która przetwarza obrazy przy użyciu zaawansowanych algorytmów i zwraca informacje o każdym obrazie. Ma warstwę bezpłatną, która zapewnia do 5000 wywołań interfejsu API w miesiącu.

1. Upewnij się, że użytkownik jest nadal zalogowany w usłudze Cloud Shell. Jeśli nie, wybierz pozycję **Wejdź w tryb koncentracji uwagi**, aby otworzyć okno usługi Cloud Shell. 

1. Utwórz nowe konto usługi Cognitive Services typu **ComputerVision** z unikatową nazwą w grupie zasobów. W przypadku warstwy bezpłatnej użyj opcji **F0** jako jednostki SKU. Jeśli masz już istniejące konto funkcji przetwarzania obrazów, może być konieczne utworzenie konta standardowego (S1), jednak może to pociągnąć za sobą pewne koszty.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Tworzenie ustawień aplikacji funkcji na potrzeby adresu URL i klucza funkcji przetwarzania obrazów

Aby wywołać interfejs API przetwarzania obrazów, wymagany jest adres URL i klucz.

1. Uzyskaj klucz i adres URL interfejsu API przetwarzania obrazów, a następnie zapisz je w zmiennych powłoki bash.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. Utwórz ustawienia aplikacji o nazwach **COMP_VISION_KEY** i **COMP_VISION_URL** w aplikacji funkcji.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>Wywoływanie interfejsu API przetwarzania obrazów z funkcji ResizeImage

W następnych krokach zmodyfikujesz funkcję **ResizeImage**, aby wywołać interfejs API przetwarzania obrazów i opisać każdy przekazany obraz oraz zapisać opis w usłudze Cosmos DB.

1. Otwórz aplikację funkcji w witrynie Azure Portal.

1. **C#**

    1. (C#) Przy użyciu panelu nawigacyjnego po lewej stronie znajdź funkcję **ResizeImage** i otwórz jej okno kodu.

    1. (C#) Zastąp kod zawartością pliku [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx). Ten kod używa metody `HttpClient` do wywoływania interfejsu API przetwarzania obrazów i zapisywania wyników funkcji w usłudze Cosmos DB.

1. **JavaScript**

    1. (JavaScript) Ta funkcja wymaga pakietu `axios` z programu npm w celu wykonywania wywołań HTTP do interfejsu API przetwarzania obrazów. Aby zainstalować pakiet npm, kliknij nazwę aplikacji funkcji w lewym obszarze nawigacji i kliknij pozycję **Funkcje platformy**.

    1. (JavaScript) Kliknij pozycję **Konsola**, aby wyświetlić okno konsoli.

    1. (JavaScript) Uruchom polecenie `npm install axios` w konsoli. Wykonanie tej operacji może potrwać minutę lub dwie.

    1. (JavaScript) Kliknij nazwę funkcji (**ResizeImage**) na lewym pasku nawigacyjnym, aby wyświetlić funkcję, a następnie zastąp całą zawartość pliku **index.js** zawartością pliku [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).

1. Kliknij pozycję **Dzienniki** poniżej okna z kodem, aby rozwinąć panel dzienników.

1. Kliknij pozycję **Zapisz**. Sprawdź panel dzienników, aby upewnić się, że funkcja została prawidłowo zapisana oraz że nie wystąpiły błędy.


## <a name="test-the-application"></a>Testowanie aplikacji

1. Otwórz aplikację w przeglądarce. Wybierz plik obrazu, a następnie go przekaż.

1. Po kilku sekundach miniatura nowego obrazu powinna pojawić się na stronie. Umieść kursor nad obrazem, aby zobaczyć opis wygenerowany przez funkcję przetwarzania obrazów.


## <a name="summary"></a>Podsumowanie

W tym module przedstawiono, jak automatycznie generować podpis dla każdego przekazanego obrazu przy użyciu interfejsu API przetwarzania obrazów w usługach Microsoft Cognitive Services. Następnie dowiesz się, jak dodać opcję uwierzytelniania do aplikacji przy użyciu uwierzytelniania usługi App Service.