---
title: Az Azure tároló beállításjegyzék hitelesítést a szolgáltatás rendszerbiztonsági tagok
description: Útmutató az Azure Active Directory szolgáltatás egyszerű biztosít hozzáférést a személyes tárolót beállításjegyzék lemezképeihez.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: marsma
ms.openlocfilehash: 2dd96880292f98d6635ae5223b34cf3b78c81e16
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Az Azure tároló beállításjegyzék hitelesítést a szolgáltatás rendszerbiztonsági tagok

Egy Azure Active Directory (Azure AD) egyszerű szolgáltatásnév segítségével adja meg a tároló kép `docker push` és `pull` a tároló beállításjegyzék elérését. Egy egyszerű szolgáltatás segítségével megadhatja a "távfelügyeleti" szolgáltatások és alkalmazások elérését.

## <a name="what-is-a-service-principal"></a>Mi az, hogy egy egyszerű szolgáltatásnév?

Az Azure AD *szolgáltatás rendszerbiztonsági tagok* az előfizetésen belüli Azure-erőforrások eléréséhez. Úgy is gondolja, hogy a szolgáltatás egyszerű, egy felhasználói identitás egy szolgáltatáshoz, ahol "szolgáltatás" bármilyen alkalmazás, szolgáltatás vagy platform, amely az erőforrásokat kell elérnie. Egy egyszerű szolgáltatást csak az érintett erőforrásokra megadott hatókörű hozzáférési jogok konfigurálható. Ezt követően konfigurálhatja az alkalmazás vagy szolgáltatás számára ezen erőforrások eléréséhez használja az egyszerű szolgáltatás hitelesítő adatait.

Azure tároló beállításjegyzék környezetében létrehozhat az Azure AD szolgáltatás egyszerű lekéréses, leküldéses és lekéréses vagy a tulajdonos engedélyekkel a személyes Docker beállításjegyzék az Azure-ban.

## <a name="why-use-a-service-principal"></a>Egy egyszerű szolgáltatás miért érdemes használni?

Az Azure AD szolgáltatás egyszerű használatával hatókörbe tartozó hozzáférés a személyes tárolót beállításjegyzék biztosíthat. Az egyes alkalmazások vagy szolgáltatások, a beállításjegyzék megfelelő hozzáférési jogosultsággal rendelkező különböző szolgáltatásnevekről hozhat létre. És elkerülheti a hitelesítő adatokat, szolgáltatások és alkalmazások közötti megosztásának, mert forgassa el a hitelesítő adatokat, vagy visszavonhatja a hozzáférést, csak az egyszerű szolgáltatásnév (és így az alkalmazás) választja.

Például a webes alkalmazás lemezképpel biztosító egyszerű szolgáltatás használható-e `pull` eléréséhez csak, amíg a buildelési rendszer használhat egy egyszerű mindkét biztosító `push` és `pull` hozzáférést. Az alkalmazások fejlesztése beavatkozás nélküli változik, ha nem befolyásolja a buildelési rendszer is forgassa el a szolgáltatás elv hitelesítő adatait.

## <a name="when-to-use-a-service-principal"></a>Egy egyszerű szolgáltatás használata

Beállításjegyzék hozzáférést biztosíthat egy szolgáltatásnevet kell használnia **távfelügyeleti forgatókönyvek**. Ez azt jelenti, hogy bármely alkalmazás, szolgáltatás vagy parancsprogram, amely kell leküldéses vagy tároló lekéréses képek a felügyelet nélküli automatizált vagy egyéb módon.

A beállításjegyzék, például amikor azt manuálisan húzza a tároló-lemezkép a fejlesztő munkaállomás egyedi hozzáférési helyette használja a saját [az Azure AD identity](container-registry-authentication.md#individual-login-with-azure-ad) a beállításjegyzék-hozzáféréshez (például a [az acr bejelentkezési][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Mintaszkriptek

Az előző mintaparancsfájlok Azure parancssori felület a Githubon, Azure PowerShell jól verzióként található:

* [Az Azure parancssori felület][acr-scripts-cli]
* [Az Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>További lépések

Miután egy egyszerű szolgáltatást, hogy van-e hozzáférést kap a tároló beállításjegyzék, használhatja az alkalmazásokat és szolgáltatásokat a beállításjegyzék beavatkozás a megszerzett hitelesítő adatokkal.

Bár ez a cikk hatókörén kívül az egyes alkalmazások szolgáltatás egyszerű hitelesítő adatok beállítása, konkrét szolgáltatások és platformok itt találhat útmutatót:

* [A hitelesítést az Azure Tárolószolgáltatás (AKS) Azure-tárolót beállításjegyzék](container-registry-auth-aks.md)
* [Azure-tárolót beállításjegyzék (ACI) Azure-tárolót példányokból hitelesítéshez](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login