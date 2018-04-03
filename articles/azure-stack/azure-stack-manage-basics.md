---
title: Az Azure verem Adminisztráció alapjai |} Microsoft Docs
description: Ismerje meg kell tudni, hogy Azure verem felügyeletéhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: mabrigg
ms.openlocfilehash: df4a5a17ad034ae5d6ab82791c020634a8758b71
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-administration-basics"></a>Az Azure verem Adminisztráció alapjai
Számos szempontot Ha most ismerkedik az Azure-verem felügyeleti tudnia kell. Ez az útmutató az Azure-verem kezelőként szerepkör áttekintése és a felhasználóknak a számukra gyorsan eredményesebbé kell biztosít.

## <a name="understand-the-builds"></a>A buildek ismertetése

### <a name="integrated-systems"></a>Integrált rendszerek

Az Azure-verem integrált rendszert használ, ha Azure verem frissített verzióit keresztül frissítési csomagok vannak osztva. Importálja ezeket a csomagokat, és alkalmazza a frissítéseket csempe a felügyeleti portál használatával.
 
### <a name="development-kit"></a>Szoftverfejlesztői készlet

Ha a csomag Azure verem használata esetén tekintse át a [Mi az Azure-verem?](.\asdk\asdk-what-is.md) tudja a csomagot, és a korlátozások célja, hogy a cikk. A csomag "védőfalat," ahol Azure verem, értékelje ki és fejlesztéséhez és tesztelni az alkalmazások nem éles környezetben célszerű használni. (Központi telepítési információk: a [Azure verem szoftverfejlesztői készlet telepítési](.\asdk\asdk-deploy.md) oktatóanyag.)

Azure, például azt innovációját annak gyorsan. Azt fogja kiadás új buildek rendszeresen. Ha a csomagot futtatja, és szeretné helyezni a legújabb buildre kell [újratelepítése veremmel Azure](.\asdk\asdk-redeploy.md). Nem alkalmazhat a frissítési csomagok. Ez a folyamat időt vesz igénybe, de az az előnye, hogy a legújabb szolgáltatásokhoz kipróbálhatja. A fejlesztői csomag dokumentációjában találhatók a webhelyen tükrözi a legfrissebb kiadott buildjét.

## <a name="learn-about-available-services"></a>További információk az elérhető szolgáltatások

Szüksége lesz egy tájékoztatási, mely szolgáltatások elérhetővé teheti a felhasználók számára. Azure verem támogatja az Azure-szolgáltatások egy részét. Támogatott szolgáltatások listájában továbbra is fejleszteni.

**Eligazodást szolgáltatások**

Alapértelmezés szerint az Azure-verem tartalmazza a következő "eligazodást szolgáltatások" Azure verem telepítésekor:

- Számítás
- Tárolás
- Hálózat
- Key Vault

Ezekkel a legalapvetőbb szolgáltatásokkal kínálhat minimális konfigurációval a felhasználók infrastruktúra,--szolgáltatás (IaaS).

**További szolgáltatások**

Jelenleg támogatott Platform,--szolgáltatás (PaaS) az alábbi kiegészítő szolgáltatásokat:

- App Service
- Azure Functions
- Az SQL és a MySQL-adatbázisok

Ezek a szolgáltatások további beállításokra van szükség, mielőtt akkor is használhatja őket a felhasználók számára. További információkért lásd: a "oktatóanyagok" és az Azure-verem operátor dokumentációban "szolgáltatások – útmutató guides\Offer" szakasza.

**Szolgáltatás terv**

Az Azure verem továbbra is az Azure-szolgáltatások támogatását. A tervezett terv, tekintse meg a [Azure verem: Azure kiterjesztése](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) tanulmány. Ugyanígy figyelheti a [Azure verem blogbejegyzések](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) új hirdetmények.

## <a name="what-account-should-i-use"></a>Milyen fiókot érdemes használni?
Nincsenek néhány fiókokkal kapcsolatos megfontolások kell ügyelnie, ha az Azure-verem kezelése. Különösen a központi telepítések segítségével a Windows Server Active Directory összevonási szolgáltatások (AD FS) helyett az Azure Active Directory (Azure AD) identitás-szolgáltatóként. A következő fiókokkal kapcsolatos megfontolások integrált Azure verem rendszerek és ASDK központi telepítéseket is vonatkozik:


|Fiók|Azure AD|AD FS|
|-----|-----|-----|
|Helyi rendszergazdai (. \Administrator)|ASDK állomás rendszergazda|ASDK állomás rendszergazda|
|AzureStack\AzureStackAdmin|ASDK állomás rendszergazda<br><br>Jelentkezzen be a verem Azure felügyeleti portálján is használható<br><br>Olvashatják és felügyelheti a Service Fabric körök|ASDK állomás rendszergazda<br><br>Nem lehet hozzáférni a verem Azure felügyeleti portálon<br><br>Olvashatják és felügyelheti a Service Fabric körök<br><br>Már nem tulajdonosa annak az alapértelmezett szolgáltató előfizetés (terjesztési pontok)|
|AzureStack\CloudAdmin|Majd futtassa a Rendszerjogosultságú végpont belül engedélyezett parancsok is|Majd futtassa a Rendszerjogosultságú végpont belül engedélyezett parancsok is<br><br>Nem jelentkezhetnek be a ASDK állomás<br><br>Az alapértelmezett szolgáltató előfizetés (terjesztési pontok) tulajdonosa|
|Azure AD globális rendszergazda|Telepítés során használt<br><br>Az alapértelmezett szolgáltató előfizetés (terjesztési pontok) tulajdonosa|Nem alkalmazható|
|

## <a name="what-tools-do-i-use-to-manage"></a>Milyen eszközök használható kezeléséhez?
 
Használhatja a [adminisztrációs portálhoz](azure-stack-manage-portals.md) vagy a PowerShell Azure verem kezeléséhez. Az alapvető fogalmakat további legkönnyebben a portálon keresztül. Ha azt szeretné, ha a PowerShell segítségével, nincsenek előkészítő lépések. Meg kell [telepítése](azure-stack-powershell-install.md) PowerShell, [letöltése](azure-stack-powershell-download.md) további modulok és [konfigurálása](azure-stack-powershell-configure-admin.md) PowerShell.

Azure verem Azure Resource Manager használ az alapul szolgáló telepítés, a felügyeleti és a szervezet mechanizmusként. Ha Azure verem kezelésére, és segítségével a felhasználók támogatására, elsajátíthatja kell erőforrás-kezelő. Tekintse meg a [Ismerkedés az Azure Resource Manager](http://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) tanulmány.

## <a name="your-typical-responsibilities"></a>A tipikus feladatkörei

A felhasználók érdemes szolgáltatásokat használni. A szempontjából a fő szerepköre számukra elérhetővé ezeket a szolgáltatásokat. Döntse el, melyik kínált szolgáltatásokat, és ezek a szolgáltatások elérhetővé terveket, ajánlatok és kvóták létrehozásával. További információkért lásd: [áttekintése Azure verem szolgáltatásokat kínáló](azure-stack-offer-services-overview.md). 

Biztosítani kell az elemek hozzáadását [a piactér](azure-stack-marketplace.md), például virtuálisgép-lemezképeket. A legegyszerűbb módja [Piactéri elemek töltse le az Azure-ból Azure verem](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Ha szeretné a tervek, ajánlatok és szolgáltatások, használja a [felhasználói portál](azure-stack-manage-portals.md); nem a felügyeleti portálon.

Szolgáltatásokat nyújtó mellett minden a rendszeres feladatok operátor az Azure verem tartsa működik, és el kell végeznie. E feladatok közé tartoznak a következők:

- Több felhasználói fiókot (a [Azure Active Directory](azure-stack-add-new-user-aad.md) központi telepítés vagy a [Active Directory összevonási szolgáltatások](azure-stack-add-users-adfs.md) központi telepítés)
- [Szerepköralapú hozzáférés-vezérlést (RBAC) szerepkörök hozzárendelése](azure-stack-manage-permissions.md) (ez nem korlátozott a rendszergazdáknak.)
- [Infrastruktúra állapotának figyelése](azure-stack-monitor-health.md)
- Kezelése [hálózati](azure-stack-viewing-public-ip-address-consumption.md) és [tárolási](azure-stack-manage-storage-accounts.md) erőforrások
- Cserélje le a hibás hardver, például [egy meghibásodott lemez cseréjéhez](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Mit kell tudniuk a felhasználóknak

Tudassa a felhasználókkal, hogyan működik a verem Azure-szolgáltatásokkal, és a környezet összekapcsolása és előfizetés az ajánlatok lesz szüksége. Az egyéni dokumentációban mellett, hogy a felhasználók számára, így érdemes lehet felhasználók számára az Azure-verem felhasználók dokumentáció hely közvetlen.

**Megtudhatja, hogyan működik a verem Azure-szolgáltatásokkal**

Nincs információ a felhasználók ismernie kell ahhoz, hogy a szolgáltatások és alkalmazások Azure-készletben. Például speciális PowerShell és az API verzió követelmények vonatkoznak. Nincsenek is, néhány funkció eltérések egy Azure-ban és az Azure-készletben egyenértékű szolgáltatás között. Győződjön meg arról, hogy a felhasználók tekintse át a következő cikkeket:

- [Kulcs szempontok: szolgáltatásokkal vagy az alkalmazások Azure verem](user/azure-stack-considerations.md)
- [Virtuális gépek Azure-készletben szempontjai](user/azure-stack-vm-considerations.md)
- [Tárolás: különbségek és szempontok](user/azure-stack-acs-differences.md)

Ezek a cikkek található információk az Azure-szolgáltatás és az Azure verem közötti eltéréseket foglalja össze. Az kiegészíti a globális Azure dokumentáció az Azure-szolgáltatások érhetők el.

**Csatlakozás Azure verem felhasználóként**

Fejlesztői csomag környezetben ha egy felhasználó nem rendelkezik a development kit gazdagép, a távoli asztal eléréséhez kell konfigurálják virtuális magánhálózati (VPN) kapcsolat Azure verem eléréséhez. Lásd: [csatlakozni az Azure verem](azure-stack-connect-azure-stack.md). 

Érdemes tudni, hogy a felhasználók hogyan [a felhasználói portál eléréséhez ](user/azure-stack-use-portal.md) vagy a Powershellen keresztüli kapcsolódás. A felhasználói portál cím integrált rendszerek környezetben üzemelő példányonként függően változik. Meg kell biztosítania a felhasználóknak a helyes URL-címet.

PowerShell használata esetén a felhasználók lehet erőforrás-szolgáltató regisztrálása szolgáltatások használatához. (Erőforrás-szolgáltató kezeli, hogy a szolgáltatás. For example, a hálózati erőforrás-szolgáltató kezeli az erőforrások, például a virtuális hálózatok, a hálózati adapterek és a terheléselosztók.) Akkor kell [telepítése](user/azure-stack-powershell-install.md) PowerShell, [letöltése](user/azure-stack-powershell-download.md) további modulok és [konfigurálása](user/azure-stack-powershell-configure-user.md) PowerShell (amely tartalmazza az erőforrás-szolgáltató regisztrálása).

**Előfizetés egy ajánlatra**

Egy felhasználó hozzáférhessen a szolgáltatásokhoz, mielőtt azok kell [ajánlatot előfizetni](azure-stack-subscribe-plan-provision-vm.md) kezelőként alkalmazásait.

## <a name="where-to-get-support"></a>Honnan szerezhetők be támogatása

### <a name="integrated-systems"></a>Integrált rendszerek

Integrált rendszer létrejön egy koordinált eszkalációs és a feloldási folyamat Microsoft eredeti hardvergyártó (OEM) hardvergyártó partnereinkkel között.

Egy felhőalapú szolgáltatások probléma esetén támogatja a Microsoft ügyfél támogatja a szolgáltatások (CSS) kínálják. Ha a felügyeleti portál jobb felső sarkában kattintson a Súgó és támogatás ikonra (kérdőjel), és kattintson a **új támogatja a kérelem**, ekkor megnyílik egy helyet, ahol közvetlenül megnyithat egy támogatási kérést.

Ha telepítési problémát, javítási és frissítési, (beleértve a mező cserélhető egység) hardver-, és bármely hardver védjegyét szoftverek, például a hardver életciklus gazdagépen futó forduljon OEM hardvergyártójához először.

Bármi más forduljon a Microsoft CSS.

### <a name="development-kit"></a>Szoftverfejlesztői készlet

A csomag, az a támogatási kapcsolatos kérdéseit teheti fel a [Microsoft fórumok](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Ha a felügyeleti portál jobb felső sarkában kattintson a Súgó és támogatás ikonra (kérdőjel), és kattintson a **új támogatja a kérelem**, ekkor megnyílik a fórumok hely közvetlenül. Ezek a fórumok rendszeresen figyeli. A csomag egy kiértékelési környezete, mert nincs Microsoft CSS kínált hivatalos támogatás.

## <a name="next-steps"></a>További lépések

- [Azure-készletben régió kezelése](azure-stack-region-management.md)


