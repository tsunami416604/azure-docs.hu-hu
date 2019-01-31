---
title: Mi az Azure AD Connect és a Connect Health. | Microsoft Docs
description: Szinkronizálását és figyelni a helyszíni környezetet az Azure ad-vel használt eszközöket írja le.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.component: hybrid
ms.date: 01/30/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: cb1ba63a0f32d026d51507fe66432a520a6f6e71
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298968"
---
# <a name="what-is-azure-ad-connect"></a>Mi az Azure AD Connect?

A Microsoft Azure AD Connect eszköze segítségével teljesítheti a hibrid identitáskezelési célokat.  Ez a tevékenység a következő jellemzőkkel bír:
    
- [A Jelszókivonat-szinkronizálás](whatis-phs.md) -egy bejelentkezési módszer, amely a felhasználók kivonatát szinkronizálja a helyszíni AD-jelszó az Azure ad-ben.
- [Az átmenő hitelesítés](how-to-connect-pta.md) -egy bejelentkezési módszer, amely lehetővé teszi a felhasználóknak az azonos jelszót a helyszínen és a felhőben, de nem igényel további infrastruktúrája az összevont környezetekben.
- [Összevonási integrációs](how-to-connect-fed-whatis.md) -összevonási az Azure AD Connect elhagyható összetevője, és a egy-egy helyszíni hibrid környezet konfigurálásához használható AD FS-infrastruktúrát. Például a tanúsítvány megújításához és a további AD FS-kiszolgálói konfigurációk mellett az AD FS felügyeleti lehetőségeket is kínál.
- [Szinkronizálási](how-to-connect-sync-whatis.md) - felhasználók, csoportok és egyéb objektumok létrehozásáért felelős.  Valamint – identitásadatokat meg arról, hogy így a helyi felhasználók és csoportok a megfelelő a felhőben.  A szinkronizálás a jelszókivonatokat is tartalmaz.
-   [Állapotfigyelés](whatis-hybrid-identity-health.md) – Az Azure AD Connect Health hatékony monitorozási képességgel rendelkezik, valamint egy központi helyet biztosít az Azure Portalon az ilyen tevékenységek megtekintéséhez. 


![Mi az az Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Mi az Azure AD Connect Health?

Az Azure Active Directory (Azure AD) Connect Health biztosít a helyszíni identitás infrastruktúra hatékony megfigyelési. Lehetővé teszi, hogy az Office 365 és a Microsoft Online Services irányuló kapcsolatok megbízható fenntartásában.  A megbízhatóságot a fontos identitás-összetevők figyelésének biztosításával érhető el. Ezenkívül adatpontjait a fontos adatokat vonatkozó ezek az összetevők könnyen elérhető.

Ez az információ az [Azure AD Connect Health portálon](https://aka.ms/aadconnecthealth) található meg. Használja az Azure AD Connect Health portálon riasztásokat, alkalmazásteljesítmény-figyelés, használatelemzési információkat és egyéb adatok megtekintéséhez. Az Azure AD Connect Health egyetlen helyre gyűjti a legfontosabb identitás-összetevők állapotadatait.

![Mi az az Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Miért érdemes az Azure AD Connect megoldást használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. Felhasználók és a szervezetek kihasználhatják:

* A felhasználók egyetlen identitással férhetnek hozzá olyan helyszíni alkalmazásokhoz és felhőszolgáltatásokhoz, mint például az Office 365.
* Mivel egyetlen eszközről van szó, a szinkronizáláshoz és bejelentkezéshez használt rendszer üzembe helyezése egyszerűen végrehajtható.
* Többféle alkalmazási helyzethez is biztosítja az elérhető legújabb képességeket. Az Azure AD Connect olyan identitásintegrációs eszközök régebbi verzióit váltja fel, mint például a DirSync és az Azure AD Sync. További információk: [Hybrid Identity directory integration tools comparison](plan-hybrid-identity-design-considerations-tools-comparison.md) (Hibrid identitás: a címtár-integrációs eszközök összehasonlítása).

## <a name="why-use-azure-ad-connect-health"></a>Miért érdemes az Azure AD Connect Health eszközt használni?
Azure AD-val a, a felhasználók hatékonyabbak, mivel a felhő- és a helyszíni erőforrások eléréséhez közös identitás áll. Kihívást a környezet nem megbízható, így a felhasználók férhetnek hozzá ezekhez az erőforrásokhoz, biztosítva lesz.  Az Azure AD Connect Health segítségével figyelheti és elemezheti a helyszíni identitás-infrastruktúrát, így az ebben a környezetben a megbízhatóságának biztosítása. Használata éppolyan egyszerű, mintha egy-egy ügynököt telepítene a helyszíni identitás-kiszolgálókra.

Az AD FS-hez készült Azure AD Connect Health a Windows Server 2008 R2, a Windows Server 2012, a Windows Server 2012 R2 és a Windows Server 2016 rendszeren támogatja az AD FS 2.0-s verzióját. Ezen kívül támogatja az AD FS proxy- vagy webalkalmazás-proxykiszolgálók figyelését is, amelyek az extranetes hozzáféréshez biztosítanak hitelesítési támogatást. Az állapotügynök egyszerűen és gyorsan végrehajtható telepítésével elérhetővé válnak az Azure AD Connect Health for AD FS legfontosabb képességei.

Főbb előnyeit és ajánlott eljárásokat:

|Főbb előnyök|Ajánlott eljárások|
|-----|-----|
|Fokozott biztonság|[Extranet zárolási tendenciák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Jelentések a sikertelen bejelentkezésekről](how-to-connect-health-adfs.md#risky-ip-report-public-preview)</br>[A megfelelő adatvédelem](reference-connect-health-user-privacy.md)|
|A riasztások [összes fontos AD FS rendszerrel kapcsolatos problémák](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Kiszolgálók konfigurálása és rendelkezésre állása</br>[Teljesítmény és csatlakozási lehetőségek](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Rendszeres karbantartás|
|Egyszerű üzembe helyezése és kezelése|[Gyors ügynök telepítése](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>Az ügynökök automatikus frissítése a legújabb verzióra</br>Az adatok perceken belül elérhetők a portálon|
Gazdag [használati metrikák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|A leggyakrabban használt alkalmazások</br>Hálózati helyek és TCP-kapcsolat</br>Jogkivonat-kérelmek kiszolgálónként|
|Nagyszerű felhasználói élményét|Az Azure Portal irányítópult stílusa</br>[Riasztások e-mailben](how-to-connect-health-adfs.md#alerts-for-ad-fs)|




## <a name="next-steps"></a>További lépések

- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Az Azure AD Connect Health-ügynökök telepítése](how-to-connect-health-agent-install.md) 
