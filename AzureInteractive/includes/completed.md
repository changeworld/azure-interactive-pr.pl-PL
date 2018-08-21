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
<span data-ttu-id="593f5-103">Pomyślnie utworzono w pełni funkcjonalną, bezserwerową aplikację przy użyciu usług platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="593f5-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="593f5-104">Oczyszczanie zasobów</span><span class="sxs-lookup"><span data-stu-id="593f5-104">Clean up resources</span></span>

<span data-ttu-id="593f5-105">Po zakończeniu tworzenia tej aplikacji możesz użyć następującego polecenia, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka:</span><span class="sxs-lookup"><span data-stu-id="593f5-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="593f5-106">Gdy pojawi się monit, wpisz `y`.</span><span class="sxs-lookup"><span data-stu-id="593f5-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="593f5-107">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="593f5-107">Next steps</span></span>

<span data-ttu-id="593f5-108">W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="593f5-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="593f5-109">Konfigurowanie usługi Azure Blob Storage w celu hostowania statycznej witryny internetowej oraz przekazywania obrazów.</span><span class="sxs-lookup"><span data-stu-id="593f5-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="593f5-110">Przekazywanie obrazów do usługi Azure Blob Storage przy użyciu usługi Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="593f5-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="593f5-111">Zmiana rozmiaru obrazów przy użyciu usługi Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="593f5-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="593f5-112">Przechowywanie metadanych obrazów w usłudze Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="593f5-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="593f5-113">Użycie interfejsu API przetwarzania obrazów usług Cognitive Services w celu automatycznego wygenerowania podpisów obrazów.</span><span class="sxs-lookup"><span data-stu-id="593f5-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="593f5-114">Użycie usługi Azure Active Directory w celu zabezpieczenia aplikacji internetowej poprzez uwierzytelnianie użytkowników.</span><span class="sxs-lookup"><span data-stu-id="593f5-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="593f5-115">Aby dowiedzieć się, jak połączyć jeszcze więcej usług z usługą Functions, przejdź do samouczka dotyczącego usługi Logic Apps.</span><span class="sxs-lookup"><span data-stu-id="593f5-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="593f5-116">Usługa Functions z usługą Logic Apps</span><span class="sxs-lookup"><span data-stu-id="593f5-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)