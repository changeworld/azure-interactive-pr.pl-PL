<span data-ttu-id="f1408-101">Usługa Azure Blob Storage to zapewniająca niskie koszty, wysoce skalowalna usługa, której można użyć do hostowania plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="f1408-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="f1408-102">W tym samouczku użyjesz tej usługi, aby obsługiwać zawartość statyczną (np. pliki HTML, JavaScript, CSS) na potrzeby tworzonej aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="f1408-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="f1408-103">Tworzenie konta usługi Storage</span><span class="sxs-lookup"><span data-stu-id="f1408-103">Create a Storage account</span></span>

<span data-ttu-id="f1408-104">Konto usługi Storage to zasób platformy Azure, który umożliwia przechowywanie tabel, kolejek, plików, obiektów blob (obiektów) oraz dysków maszyn wirtualnych.</span><span class="sxs-lookup"><span data-stu-id="f1408-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="f1408-105">Zaloguj się w usłudze Cloud Shell (Bash), wybierając przycisk **Wejdź w tryb koncentracji uwagi**.</span><span class="sxs-lookup"><span data-stu-id="f1408-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="f1408-106">Ten przycisk znajduje się w prawym górnym rogu lub u dołu strony, w zależności od tego, jak szerokie jest okno przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="f1408-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="f1408-107">Tryb koncentracji uwagi dokuje okno usługi Cloud Shell po prawej stronie od okna przeglądarki, dzięki czemu można łatwo wykonywać polecenia przedstawiane w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="f1408-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="f1408-108">Na platformie Azure grupa zasobów to kontener przechowujący zasoby związane z platformą Azure, aby ułatwić zarządzanie.</span><span class="sxs-lookup"><span data-stu-id="f1408-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="f1408-109">Utwórz nową grupę zasobów o nazwie **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="f1408-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="f1408-110">Zawartość statyczna (pliki HTML, CSS i JavaScript) na potrzeby tego samouczka jest hostowana w usłudze Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="f1408-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="f1408-111">Usługa Blob Storage wymaga konta usługi Storage.</span><span class="sxs-lookup"><span data-stu-id="f1408-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="f1408-112">Utwórz konto usługi Storage (ogólnego przeznaczenia w wersji 2) w grupie zasobów.</span><span class="sxs-lookup"><span data-stu-id="f1408-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="f1408-113">Zastąp `<storage account name>` unikatową nazwą.</span><span class="sxs-lookup"><span data-stu-id="f1408-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="f1408-114">Korzystając z paska wyszukiwania w górnej części witryny [Azure Portal](https://portal.azure.com), znajdź właśnie utworzone konto magazynu i otwórz je.</span><span class="sxs-lookup"><span data-stu-id="f1408-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="f1408-115">Na lewym pasku nawigacyjnym wybierz opcję **Statyczna witryna internetowa (wersja zapoznawcza)**, aby skonfigurować kontener na potrzeby hostowania statycznej witryny internetowej.</span><span class="sxs-lookup"><span data-stu-id="f1408-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="f1408-116">Wybierz opcję **Włączone**, aby włączyć statyczną witrynę internetową.</span><span class="sxs-lookup"><span data-stu-id="f1408-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="f1408-117">Wprowadź *index.html* jako nazwę dokumentu indeksu.</span><span class="sxs-lookup"><span data-stu-id="f1408-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="f1408-118">Pole zawiera już szary tekst *index.html*, ale jest to wyłącznie tekst przykładowy; nadal należy wprowadzić wartość w polu.</span><span class="sxs-lookup"><span data-stu-id="f1408-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="f1408-119">Kliknij pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="f1408-119">Click **Save**.</span></span>
    
    ![Wprowadzanie ustawień statycznej witryny internetowej](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="f1408-121">Zapisz **podstawowy punkt końcowy** w miejscu, z którego możesz wygodnie go skopiować podczas pracy z samouczkiem.</span><span class="sxs-lookup"><span data-stu-id="f1408-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="f1408-122">Jest to adres URL aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="f1408-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="f1408-123">Przekazywanie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="f1408-123">Upload the web application</span></span>

1. <span data-ttu-id="f1408-124">Pliki źródłowe aplikacji, którą tworzysz w tym samouczku, znajdują się w [repozytorium GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span><span class="sxs-lookup"><span data-stu-id="f1408-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="f1408-125">Upewnij się, że znajdujesz się w katalogu głównym w usłudze Cloud Shell, a następnie sklonuj to repozytorium.</span><span class="sxs-lookup"><span data-stu-id="f1408-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="f1408-126">Repozytorium jest klonowane do lokalizacji `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="f1408-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="f1408-127">Aplikacja internetowa po stronie klienta znajduje się w folderze **www** i jest kompilowana przy użyciu struktury Vue.js JavaScript.</span><span class="sxs-lookup"><span data-stu-id="f1408-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="f1408-128">Przejdź do folderu i uruchom polecenia npm, aby zainstalować zależności aplikacji oraz skompilować aplikację.</span><span class="sxs-lookup"><span data-stu-id="f1408-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="f1408-129">Wykonanie ostatniego z tych poleceń może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="f1408-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="f1408-130">Aplikacja jest generowana w folderze **dist**.</span><span class="sxs-lookup"><span data-stu-id="f1408-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="f1408-131">Zmień bieżący katalog na folder **dist** i przekaż aplikację do kontenera obiektów blob **$web**.</span><span class="sxs-lookup"><span data-stu-id="f1408-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="f1408-132">Otwórz adres URL podstawowego punktu końcowego statycznych witryn internetowych usługi Storage w przeglądarce internetowej, aby wyświetlić aplikację.</span><span class="sxs-lookup"><span data-stu-id="f1408-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Strona główna pierwszej bezserwerowej aplikacji internetowej](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="f1408-134">Podsumowanie</span><span class="sxs-lookup"><span data-stu-id="f1408-134">Summary</span></span>

<span data-ttu-id="f1408-135">W tym module utworzono grupę zasobów o nazwie **first-serverless-app** zawierającą konto usługi Storage.</span><span class="sxs-lookup"><span data-stu-id="f1408-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="f1408-136">Kontener obiektów blob o nazwie **$web** na koncie usługi Storage przechowuje zawartość statyczną aplikacji internetowej oraz udostępnia zawartość publicznie.</span><span class="sxs-lookup"><span data-stu-id="f1408-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="f1408-137">Dalej dowiesz się, jak używać funkcji bezserwerowej, aby przekazywać obrazy do magazynu obiektów blob z tej aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="f1408-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
