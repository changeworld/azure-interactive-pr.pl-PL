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
<span data-ttu-id="83709-103">W tym samouczku wdrożysz prostą aplikację internetową, która przedstawia interfejs użytkownika oparty na kodzie HTML.</span><span class="sxs-lookup"><span data-stu-id="83709-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="83709-104">Zaplecze bezserwerowe umożliwia aplikacji przekazywanie obrazów oraz automatyczne pobieranie podpisów opisujących te obrazy.</span><span class="sxs-lookup"><span data-stu-id="83709-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Uruchamianie aplikacji internetowej](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="83709-106">Na poniższym diagramie przedstawiono usługi platformy Azure używane przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="83709-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="83709-107">Usługa Blob Storage obsługuje statyczną zawartość internetową (pliki HTML, CSS, JS) oraz przechowuje obrazy.</span><span class="sxs-lookup"><span data-stu-id="83709-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="83709-108">Usługa Azure Functions zarządza przekazywaniem i zmianą rozmiaru obrazów oraz przechowywaniem metadanych.</span><span class="sxs-lookup"><span data-stu-id="83709-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="83709-109">Usługa Cosmos DB przechowuje metadane obrazów.</span><span class="sxs-lookup"><span data-stu-id="83709-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="83709-110">Usługa Logic Apps pobiera podpisy obrazów z interfejsu API przetwarzania obrazów.</span><span class="sxs-lookup"><span data-stu-id="83709-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="83709-111">Usługa Azure Active Directory zarządza uwierzytelnianiem użytkowników.</span><span class="sxs-lookup"><span data-stu-id="83709-111">Azure Active Directory manages user authentication.</span></span>

![Diagram architektury rozwiązania](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="83709-113">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="83709-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="83709-114">Konfigurowanie usługi Azure Blob Storage w celu hostowania statycznej witryny internetowej oraz przekazywania obrazów.</span><span class="sxs-lookup"><span data-stu-id="83709-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="83709-115">Przekazywanie obrazów do usługi Azure Blob Storage przy użyciu usługi Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="83709-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="83709-116">Zmiana rozmiaru obrazów przy użyciu usługi Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="83709-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="83709-117">Przechowywanie metadanych obrazów w usłudze Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="83709-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="83709-118">Użycie interfejsu API przetwarzania obrazów usług Cognitive Services w celu automatycznego wygenerowania podpisów obrazów.</span><span class="sxs-lookup"><span data-stu-id="83709-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="83709-119">Użycie usługi Azure Active Directory w celu zabezpieczenia aplikacji internetowej poprzez uwierzytelnianie użytkowników.</span><span class="sxs-lookup"><span data-stu-id="83709-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>