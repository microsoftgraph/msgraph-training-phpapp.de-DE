<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="42a86-101">In diesem Tutorial erfahren Sie, wie Sie eine PHP-Web-App erstellen, die die Microsoft Graph-API zum Abrufen von Kalenderinformationen für einen Benutzer verwendet.</span><span class="sxs-lookup"><span data-stu-id="42a86-101">This tutorial teaches you how to build a PHP web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="42a86-102">Wenn Sie das fertige Tutorial lieber herunterladen möchten, können Sie es auf zweierlei Weise herunterladen.</span><span class="sxs-lookup"><span data-stu-id="42a86-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="42a86-103">Laden Sie den [php-Schnellstart](https://developer.microsoft.com/graph/quick-start?platform=option-php) herunter, um in wenigen Minuten Arbeitscode zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="42a86-103">Download the [PHP quick start](https://developer.microsoft.com/graph/quick-start?platform=option-php) to get working code in minutes.</span></span>
> - <span data-ttu-id="42a86-104">Laden oder Klonen Sie das [GitHub-Repository](https://github.com/microsoftgraph/msgraph-training-phpapp).</span><span class="sxs-lookup"><span data-stu-id="42a86-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-phpapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="42a86-105">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="42a86-105">Prerequisites</span></span>

<span data-ttu-id="42a86-106">Bevor Sie mit diesem Lernprogramm beginnen, sollten Sie [php](http://php.net/downloads.php), [Composer](https://getcomposer.org/)und [Laravel](https://laravel.com/) auf dem Entwicklungscomputer installiert haben.</span><span class="sxs-lookup"><span data-stu-id="42a86-106">Before you start this tutorial, you should have [PHP](http://php.net/downloads.php), [Composer](https://getcomposer.org/), and [Laravel](https://laravel.com/) installed on your development machine.</span></span>

> [!NOTE]
> <span data-ttu-id="42a86-107">Dieses Tutorial wurde mit PHP Version 7,2 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="42a86-107">This tutorial was written with PHP version 7.2.</span></span> <span data-ttu-id="42a86-108">Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, jedoch nicht getestet.</span><span class="sxs-lookup"><span data-stu-id="42a86-108">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="42a86-109">Feedback</span><span class="sxs-lookup"><span data-stu-id="42a86-109">Feedback</span></span>

<span data-ttu-id="42a86-110">Feedback zu diesem Tutorial finden Sie im GitHub- [Repository](https://github.com/microsoftgraph/msgraph-training-phpapp).</span><span class="sxs-lookup"><span data-stu-id="42a86-110">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-phpapp).</span></span>