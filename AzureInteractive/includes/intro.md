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
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079457"
---
W tym samouczku wdrożysz prostą aplikację internetową, która przedstawia interfejs użytkownika oparty na kodzie HTML. Zaplecze bezserwerowe umożliwia aplikacji przekazywanie obrazów oraz automatyczne pobieranie podpisów opisujących te obrazy.

![Uruchamianie aplikacji internetowej](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

Na poniższym diagramie przedstawiono usługi platformy Azure używane przez aplikację:

1. Usługa Blob Storage obsługuje statyczną zawartość internetową (pliki HTML, CSS, JS) oraz przechowuje obrazy.
2. Usługa Azure Functions zarządza przekazywaniem i zmianą rozmiaru obrazów oraz przechowywaniem metadanych.
3. Usługa Cosmos DB przechowuje metadane obrazów.
4. Usługa Logic Apps pobiera podpisy obrazów z interfejsu API przetwarzania obrazów.
5. Usługa Azure Active Directory zarządza uwierzytelnianiem użytkowników.

![Diagram architektury rozwiązania](media/functions-first-serverless-web-app/0-architecture.jpg)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie usługi Azure Blob Storage w celu hostowania statycznej witryny internetowej oraz przekazywania obrazów.
> * Przekazywanie obrazów do usługi Azure Blob Storage przy użyciu usługi Azure Functions.
> * Zmiana rozmiaru obrazów przy użyciu usługi Azure Functions.
> * Przechowywanie metadanych obrazów w usłudze Azure Cosmos DB.
> * Użycie interfejsu API przetwarzania obrazów usług Cognitive Services w celu automatycznego wygenerowania podpisów obrazów.
> * Użycie usługi Azure Active Directory w celu zabezpieczenia aplikacji internetowej poprzez uwierzytelnianie użytkowników.