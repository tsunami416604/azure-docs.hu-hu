---
title: Az Azure Stackben API-verzióprofilok kezelése |} A Microsoft Docs
description: További információ az Azure Stackben API-verzióprofilok.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: ba8778bad9ed1b8ec095da084ffcec79658bdea8
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060614"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Az Azure Stackben API-verzióprofilok kezelése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

API-profilok adja meg, az Azure erőforrás-szolgáltató és az Azure REST-végpontokat az API-verzió. Létrehozhat egyéni ügyfelek különböző nyelveken, API-profilok használatával. Minden ügyfél egy API-profilt használja a megfelelő erőforrás-szolgáltató és az API-verzió forduljon az Azure Stackhez készült.

Létrehozhat egy alkalmazást az Azure erőforrás-szolgáltatók dolgozhat ki, hogy pontosan mely mindegyik erőforrás-szolgáltató API verziója kompatibilis az Azure Stack rendezés nélkül. Az alkalmazás-profilhoz; csak igazítása Az SDK-t a megfelelő API-verzió vált át.

Ez a témakör segítségével:

 - API-t a profilok megismerése az Azure Stackhez.
 - Ismerje meg, hogyan használhat API-t, a megoldások fejlesztése.
 - Tekintse meg, hogy hol található a kód jellemző útmutatást.

## <a name="summary-of-api-profiles"></a>API-profilok összefoglalása

- API-profilok segítségével az Azure erőforrás-szolgáltatók és a saját API-verziók jelölik.
- API-profilokat hozhat létre a sablonok között több Azure-felhők jöttek létre. Profilok felel meg az igényeknek, kompatibilis és állandó kapcsolat célja.
- Profilok évente négy alkalommal jelennek meg.
- Három profilt elnevezési konvenciókat használja:
    - **legfrissebb**  
        Tartalmazza a legújabb API-verziók, amely a globális Azure-ban.
    - **yyyy-mm-dd-hybrid**  
    Kiadás dátuma: egy róla kiadása ütemben történik, ebben a kiadásban összpontosít és stabilabbá teheti több felhő között. Ez a profil optimális Azure Stack-kompatibilitási célozza.
    - **éééé-hh-nn-profil** optimális stabilitás és a legújabb funkciók között helyezkedik el.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Az Azure API-profilok és az Azure Stack-kompatibilitás

A legújabb Azure API-profilok nem kompatibilisek az Azure Stack használatával. A következő tanúsítványelnevezési módszerek segítségével azonosíthatja a profilok használata az Azure Stack-megoldások számára.

**legfrissebb**  
Ez a profil a legújabb API-verziók található a globális Azure-t, amely nem fog működni az Azure Stack rendelkezik. **Legújabb** kompatibilitástörő változásokat legnagyobb száma. A profil fenntartott helyezi, stabilitás és más felhőkben való kompatibilitás érdekében. Ha a legújabb API-verziókat használhat **legújabb** a profilt kell használnia.

**éééé-hh-nn-hibrid**  
Ez a profil jelent márciusi és szeptemberi frissítések minden évben. Ezzel a profillal rendelkezik, optimális stabilitását és a különböző felhőkben való kompatibilitás érdekében. **Éééé-hh-nn-hibrid** célja, hogy a cél a globális Azure és az Azure Stackben. Az Azure API-verziói ehhez a profilhoz lesznek ugyanaz, mint az Azure Stacken felsorolt azokat. Ez a profil használatával kód a hibrid felhőalapú megoldások fejlesztése.

**yyyy-mm-dd-profile**  
Ez a profil jelent meg az Azure globális június és December. Ezt a profilt nem fognak működni az Azure Stack; ellen általában lesz számos kompatibilitástörő változásokat. Bár ez optimális stabilitását és a legújabb funkciók, a különbség a között helyezkedik el **legújabb** , és ezt a profilt, amely **legújabb** mindig a legújabb API-verziók, függetlenül attól, hogy mikor fog állni az API-hoz fel lett oldva. Például ha egy új API-verzió holnap létrehozása a Compute API-hoz, hogy API-verzió jelenik a **legújabb**, azonban nem a **éééé-hh-nn-profil** mert ehhez a profilhoz már létezik.  **éééé-hh-nn-profil** június és December előtt kiadott a legújabb verziókat ismerteti.

## <a name="azure-resource-manager-api-profiles"></a>Az Azure Resource Manager API-profilok

Az Azure Stack nem használja a globális Azure-ban található API-verziók legújabb verzióját. Egy megoldás létrehozásakor meg kell keresnie az API-verziót minden egyes Azure erőforrás-szolgáltató, amely kompatibilis az Azure Stack használatával.

Inkább, mint a minden erőforrás-szolgáltató és az Azure Stack által támogatott verziót kutatás, használhat egy API-profilt. A profil az erőforrás-szolgáltatók és API-verziók egy halmazát határozza meg. Az SDK-t vagy egy eszközt, az SDK visszaáll a cél api-verzió a profilban megadott. Az API-profiljaival megadhatja a profil verziója, amely egy teljes sablon vonatkozik, és futásidőben, az Azure Resource Manager választja ki a megfelelő verziót az erőforrás.

API-profilok, amelyek használják az Azure Resource Managerrel, például a PowerShell, az Azure CLI, az SDK-t mind a Microsoft Visual Studio code tools együttműködve. Eszközök és SDK-k segítségével profilok olvassa el a modulok és a egy application készítése során alkalmazandó kódtárak melyik verzióját.

Például, ha a PowerShell segítségével hozzon létre egy tárfiókot használja a fiók a **Microsoft.Storage** erőforrás-szolgáltató, amely támogatja a 2016-03-30 api-verzió és a egy virtuális Gépet a Microsoft.Compute erőforrás-szolgáltató használatával a 2015-12-01-es api-verzióval rendelkező , keresse ki az PowerShell-modul támogatja a 2016-03-30 Storage kell, és mely modul támogatja a 2015-02-01-es számítási és telepítheti azokat. Ehelyett egy profil is használhatja. A parancsmag ** telepítés-profil * profilename x és PowerShell betölti a modulokat megfelelő verzióját.

Ehhez hasonlóan ha Python-alapú alkalmazás készítése a Python SDK-t használja, megadhatja a profil. Az SDK-t a parancsfájlban megadott erőforrás-szolgáltatókat betölti a megfelelő modulokat.

A fejlesztők összpontosíthat a megoldás írása. Ahelyett, hogy melyik api-verziók, erőforrás-szolgáltató kutatást és melyik felhő működik együtt, egy profilt használ, és tudja, hogy a kód minden felhőkben, amelyek támogatják a profil működni fog-e.

## <a name="api-profile-code-samples"></a>API-profil-Kódminták

Megtalálhatja a segítséget nyújtanak a választott nyelven az Azure Stack-profilok használatával a megoldás integrálása Kódminták. Jelenleg található útmutatás és példák a következő nyelveken:

- **PowerShell**  
Használhatja a **AzureRM.Bootstrapper** modul elérhető a PowerShell-galériából, a PowerShell-parancsmagok használata API-verzióprofilok kell lekérni. További információ: [használata API-verzióprofilok PowerShell](azure-stack-version-profiles-powershell.md).
- **Azure CLI 2.0**  
Frissítheti az Ön környezetének konfigurációját az Azure Stack meghatározott API verzió profil használatára. További információ: [használata API-verzióprofilok Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
A GO SDK egy profil a különböző szolgáltatásokhoz különböző verziójú különböző erőforrástípusok kombinációját. a profilok alatt érhetők el profilok / elérési út, a verziójuk a **éééé-hh-nn** formátumban. További információ: [használata API-verzióprofilok GÓHOZ készült](azure-stack-version-profiles-go.md).
- **Ruby**  
A Ruby SDK az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK-t az erőforrás-szolgáltató például számítási, a virtuális hálózatok és a tárolási Ruby nyelven. További információ: [használata API-verzióprofilok Ruby használatával](azure-stack-version-profiles-ruby.md)
- **Python**  
- A Python SDK API-verzióprofilok célozhat meg például az Azure Stack és a globális Azure más felhőalapú platformot támogatja. Használhat API-t egy hibrid felhőalapú megoldások létrehozásához. További információ: [használata API-verzióprofilok Python használatával](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)
* [Tekintse át a profilok által támogatott erőforrás-szolgáltató API-ja verziókra](azure-stack-profiles-azure-resource-manager-versions.md).