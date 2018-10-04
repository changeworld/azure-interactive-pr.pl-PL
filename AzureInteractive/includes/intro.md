---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460028"
---
<span data-ttu-id="74b1c-103">W tym samouczku wdrożysz prostą aplikację internetową, która przedstawia interfejs użytkownika oparty na kodzie HTML.</span><span class="sxs-lookup"><span data-stu-id="74b1c-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="74b1c-104">Zaplecze bezserwerowe umożliwia aplikacji przekazywanie obrazów oraz automatyczne pobieranie podpisów opisujących te obrazy.</span><span class="sxs-lookup"><span data-stu-id="74b1c-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Uruchamianie aplikacji internetowej](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="74b1c-106">Na poniższym diagramie przedstawiono usługi platformy Azure używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="74b1c-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="74b1c-107">Usługa Blob Storage obsługuje statyczną zawartość internetową (pliki HTML, CSS, JS) oraz przechowuje obrazy.</span><span class="sxs-lookup"><span data-stu-id="74b1c-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="74b1c-108">Usługa Azure Functions zarządza przekazywaniem i zmianą rozmiaru obrazów oraz przechowywaniem metadanych.</span><span class="sxs-lookup"><span data-stu-id="74b1c-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="74b1c-109">Usługa Cosmos DB przechowuje metadane obrazów.</span><span class="sxs-lookup"><span data-stu-id="74b1c-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="74b1c-110">Usługa Logic Apps pobiera podpisy obrazów z interfejsu API przetwarzania obrazów.</span><span class="sxs-lookup"><span data-stu-id="74b1c-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="74b1c-111">Usługa Azure Active Directory zarządza uwierzytelnianiem użytkowników.</span><span class="sxs-lookup"><span data-stu-id="74b1c-111">Azure Active Directory manages user authentication.</span></span>

![Diagram architektury rozwiązania](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="74b1c-113">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="74b1c-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="74b1c-114">Konfigurowanie usługi Azure Blob Storage w celu hostowania statycznej witryny internetowej oraz przekazywania obrazów.</span><span class="sxs-lookup"><span data-stu-id="74b1c-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="74b1c-115">Przekazywanie obrazów do usługi Azure Blob Storage przy użyciu usługi Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="74b1c-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="74b1c-116">Zmiana rozmiaru obrazów przy użyciu usługi Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="74b1c-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="74b1c-117">Przechowywanie metadanych obrazów w usłudze Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="74b1c-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="74b1c-118">Użycie interfejsu API przetwarzania obrazów usług Cognitive Services w celu automatycznego wygenerowania podpisów obrazów.</span><span class="sxs-lookup"><span data-stu-id="74b1c-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="74b1c-119">Użycie usługi Azure Active Directory w celu zabezpieczenia aplikacji internetowej poprzez uwierzytelnianie użytkowników.</span><span class="sxs-lookup"><span data-stu-id="74b1c-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>
