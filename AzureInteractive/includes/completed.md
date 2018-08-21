---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079480"
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