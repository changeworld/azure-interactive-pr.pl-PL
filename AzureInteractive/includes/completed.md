---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460005"
---
Pomyślnie utworzono w pełni funkcjonalną, bezserwerową aplikację przy użyciu usług platformy Azure.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Po zakończeniu tworzenia tej aplikacji możesz użyć następującego polecenia, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka:

```azurecli
az group delete --name first-serverless-app
```

Gdy pojawi się monit, wpisz `y`.  

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie usługi Azure Blob Storage w celu hostowania statycznej witryny internetowej oraz przekazywania obrazów.
> * Przekazywanie obrazów do usługi Azure Blob Storage przy użyciu usługi Azure Functions.
> * Zmiana rozmiaru obrazów przy użyciu usługi Azure Functions.
> * Przechowywanie metadanych obrazów w usłudze Azure Cosmos DB.
> * Użycie interfejsu API przetwarzania obrazów usług Cognitive Services w celu automatycznego wygenerowania podpisów obrazów.
> * Użycie usługi Azure Active Directory w celu zabezpieczenia aplikacji internetowej poprzez uwierzytelnianie użytkowników.

Aby dowiedzieć się, jak połączyć jeszcze więcej usług z usługą Functions, przejdź do samouczka dotyczącego usługi Logic Apps. 

> [!div class="nextstepaction"]
> [Usługa Functions z usługą Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
