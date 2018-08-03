---
title: A szolgáltatásnevek Azure Tárolóregisztrációs adatbázis hitelesítési
description: Megtudhatja, hogyan biztosíthat a hozzáférést a privát tárolójegyzékben található rendszerképek az Azure Active Directory egyszerű szolgáltatás használatával.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: marsma
ms.openlocfilehash: ffdf8af805ce7e5068ceef9a4b265ea00d36fc79
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39448009"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>A szolgáltatásnevek Azure Tárolóregisztrációs adatbázis hitelesítési

Az Azure Active Directory (Azure AD) egyszerű szolgáltatás használatával adja meg a tároló rendszerképét `docker push` és `pull` hozzáférés a tárolóregisztrációs adatbázisba. Egyszerű szolgáltatás használatával megadhatja a "távfelügyelt" szolgáltatások és alkalmazások elérését.

## <a name="what-is-a-service-principal"></a>Mi az egyszerű szolgáltatás?

Az Azure AD *egyszerű szolgáltatások* az adott előfizetéshez tartozó Azure-erőforrásokhoz való hozzáférés biztosítása. Is felfoghatók szolgáltatás egyszerű, egy felhasználói identitás egy szolgáltatáshoz, ahol a "szolgáltatás" minden olyan alkalmazás, szolgáltatás vagy a platform, amely az erőforrásokat kell elérnie. A hozzáférési jogosultságok hatóköre csak azokhoz az erőforrásokhoz megad egy egyszerű szolgáltatás segítségével konfigurálhat. Ezt követően konfigurálhatja úgy az alkalmazást vagy szolgáltatást a szolgáltatásnév hitelesítő adatainak használata az erőforrások.

Azure Container Registry környezetében létrehozhat egy Azure AD egyszerű szolgáltatás lekéréses, leküldéses és lekéréses vagy tulajdonosi engedélyekkel rendelkező a privát Docker-tárolójegyzék az Azure-ban.

## <a name="why-use-a-service-principal"></a>Miért érdemes használni egy egyszerű szolgáltatást?

Az Azure AD egyszerű szolgáltatás használatával megadhat hatókörrel rendelkező hozzáférést a privát tárolóregisztrációs adatbázisba. Létrehozhat különböző szolgáltatásnevek minden, az alkalmazások és szolgáltatások, minden testre szabott hozzáférési jogosultságokkal a tárolójegyzékbe. És elkerülheti a hitelesítő adatok, szolgáltatások és alkalmazások közötti megosztásának, mert hitelesítő adatok forgatása, vagy csak az egyszerű szolgáltatás (és így az alkalmazás) úgy dönt, a hozzáférés visszavonásához.

Például a webes alkalmazások használhatja, amely képet ad egyszerű szolgáltatás `pull` eléréséhez csak, míg a buildelési rendszer használhatja, amely biztosít is egyszerű szolgáltatás `push` és `pull` hozzáférést. Ha az alkalmazás fejlesztésének teljes megváltozik, egyszerű szolgáltatás hitelesítő adatainak működésének megzavarása nélkül megtesztelheti az összeállítási rendszer elfordításával.

## <a name="when-to-use-a-service-principal"></a>Egyszerű szolgáltatás használata

Beállításjegyzék hozzáférést biztosíthat egy egyszerű szolgáltatást kell használnia **távfelügyelt forgatókönyvek**. Vagyis minden olyan alkalmazás, szolgáltatás vagy parancsfájlt, amely leküldéses kell, vagy kérje le a tároló képek a felügyelet nélküli automatikus vagy egyéb módon.

Egyéni hozzáférési egy regisztrációs adatbázisba, mint ha manuálisan lekéri egy tárolórendszerképet a fejlesztő munkaállomás, hogy inkább használja a saját [az Azure AD identity](container-registry-authentication.md#individual-login-with-azure-ad) a beállításjegyzék-hozzáféréshez (például [az acr bejelentkezési][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Mintaszkriptek

Jól verzióként az Azure PowerShell előző mintaszkriptek találja meg a Githubon, az Azure CLI-hez:

* [Az Azure CLI][acr-scripts-cli]
* [Azure PowerShell-lel][acr-scripts-psh]

## <a name="next-steps"></a>További lépések

Ha már rendelkezik egy egyszerű szolgáltatást, hogy Ön már hozzáférést kap a tárolóregisztrációs adatbázisba, használhatja az alkalmazásokat és szolgáltatásokat a beállításjegyzék-interakció a saját hitelesítő adatait.

Bár ez a cikk hatókörén kívül az egyes alkalmazások általi használatát a szolgáltatásnév hitelesítő adatainak konfigurálása, konkrét szolgáltatások és platformok itt találhat útmutatót:

* [Hitelesítés az Azure tároló-beállításjegyzéket az Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)
* [Hitelesítés az Azure tároló-beállításjegyzéket az Azure Container Instances (aci Szolgáltatásban)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login