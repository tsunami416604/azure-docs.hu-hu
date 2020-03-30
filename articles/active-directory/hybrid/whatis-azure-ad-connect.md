---
title: Mi az Azure AD Connect és a Connect Health. | Microsoft Docs
description: A helyszíni környezet és az Azure AD szinkronizálásához és figyeléséhez használt eszközök ismertetése.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11c57065e21c48a23148cacd91a42737edc13538
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049412"
---
# <a name="what-is-azure-ad-connect"></a>Mi az Azure AD Connect?

A Microsoft Azure AD Connect eszköze segítségével teljesítheti a hibrid identitáskezelési célokat.  Ez a tevékenység a következő jellemzőkkel bír:
     
- [Jelszó-kivonat-szinkronizálás](whatis-phs.md) – Olyan bejelentkezési módszer, amely szinkronizálja a helyszíni AD-jelszavak kivonatát az Azure AD-vel.
- [Átmenő hitelesítés](how-to-connect-pta.md) – Olyan bejelentkezési módszer, amely lehetővé teszi a felhasználók számára, hogy ugyanazt a jelszót használják a helyszínen és a felhőben, de nem igényel egy összevont környezet további infrastruktúráját.
- [Összevonási integráció](how-to-connect-fed-whatis.md) – Az összevonás az Azure AD Connect opcionális része, és egy helyszíni AD FS-infrastruktúra használatával hibrid környezet konfigurálására használható. Az AD FS felügyeleti lehetőségeket is biztosít, például a tanúsítványok megújítását és az AD FS-kiszolgáló további telepítését.
- [Szinkronizálás –](how-to-connect-sync-whatis.md) felelős a felhasználók, csoportok és egyéb objektumok létrehozásáért.  A helyszíni felhasználók és csoportok identitásadatainak biztosítása is megfelel a felhőnek.  Ez a szinkronizálás jelszókivét is tartalmaz.
-      [Állapotfigyelés](whatis-hybrid-identity-health.md) – Az Azure AD Connect Health robusztus figyelést biztosíthat, és központi helyet biztosít az Azure Portalon a tevékenység megtekintéséhez. 


![Mi az az Azure AD Connect?](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Mi az Azure AD Connect Health?

Az Azure Active Directory (Azure AD) Connect Health a helyszíni identitásinfrastruktúra megbízható figyelését biztosítja. Lehetővé teszi, hogy megbízható kapcsolatot tartson fenn az Office 365-tel és a Microsoft Online Services szolgáltatással.  Ez a megbízhatóság úgy érhető el, hogy figyelési lehetőségeket biztosít a kulcsfontosságú identitás-összetevőkhöz. Emellett könnyen hozzáférhetővé teszi az összetevőkre vonatkozó legfontosabb adatpontokat.

Ez az információ az [Azure AD Connect Health portálon](https://aka.ms/aadconnecthealth) található meg. Az Azure AD Connect Health portálhasználatával megtekintheti a riasztásokat, a teljesítményfigyelést, a használati elemzéseket és egyéb információkat. Az Azure AD Connect Health egyetlen helyre gyűjti a legfontosabb identitás-összetevők állapotadatait.

![Mi az az Azure AD Connect Health?](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Miért érdemes az Azure AD Connect megoldást használni?
A helyszíni címtárak és az Azure AD integrálása révén a felhasználók munkája hatékonyabbá válik, mivel a felhőalapú és a helyszíni erőforrások hozzáféréséhez közös identitás áll a rendelkezésükre. A felhasználók és szervezetek a következőket vehetik igénybe:

* A felhasználók egyetlen identitással férhetnek hozzá olyan helyszíni alkalmazásokhoz és felhőszolgáltatásokhoz, mint például az Office 365.
* Mivel egyetlen eszközről van szó, a szinkronizáláshoz és bejelentkezéshez használt rendszer üzembe helyezése egyszerűen végrehajtható.
* Többféle alkalmazási helyzethez is biztosítja az elérhető legújabb képességeket. Az Azure AD Connect lecseréli az identitásintegrációs eszközök, például a DirSync és az Azure AD Sync régebbi verzióit. További információ: [Hibrid identitáskönyvtár-integrációs eszközök összehasonlítása](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Miért érdemes az Azure AD Connect Health eszközt használni?
Az Azure AD-vel a felhasználók hatékonyabbak, mivel a felhőbeli és a helyszíni erőforrások hoz nak hozzáférést egyaránt. A környezet megbízhatóságának biztosítása, hogy a felhasználók hozzáférhessenek ezekhez az erőforrásokhoz, kihívást jelent.  Az Azure AD Connect Health segítségével figyelheti és betekintést nyerhet a helyszíni identitás-infrastruktúrába, így biztosítva a környezet megbízhatóságát. Használata éppolyan egyszerű, mintha egy-egy ügynököt telepítene a helyszíni identitás-kiszolgálókra.

Az AD FS-hez készült Azure AD Connect Health a Windows Server 2008 R2, a Windows Server 2012, a Windows Server 2012 R2 és a Windows Server 2016 rendszeren támogatja az AD FS 2.0-s verzióját. Ezen kívül támogatja az AD FS proxy- vagy webalkalmazás-proxykiszolgálók figyelését is, amelyek az extranetes hozzáféréshez biztosítanak hitelesítési támogatást. Az állapotügynök egyszerűen és gyorsan végrehajtható telepítésével elérhetővé válnak az Azure AD Connect Health for AD FS legfontosabb képességei.

Legfontosabb előnyök és bevált gyakorlatok:

|Főbb előnyök|Ajánlott eljárások|
|-----|-----|
|Fokozott biztonság|[Extranet zárolási tendenciák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Jelentések a sikertelen bejelentkezésekről](how-to-connect-health-adfs-risky-ip.md)</br>[Az adatvédelemmel kompatibilis](reference-connect-health-user-privacy.md)|
|Riasztás az [ADFS rendszer minden kritikus problémája esetén](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Kiszolgálók konfigurálása és rendelkezésre állása</br>[Teljesítmény és csatlakozási lehetőségek](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Rendszeres karbantartás|
|Könnyen telepíthető és kezelhető|[Gyorsügynök telepítése](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>Az ügynökök automatikus frissítése a legújabb verzióra</br>Az adatok perceken belül elérhetők a portálon|
Részletes [használati metrikák](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|A leggyakrabban használt alkalmazások</br>Hálózati helyek és TCP-kapcsolat</br>Jogkivonat-kérelmek kiszolgálónként|
|Nagyszerű felhasználói élmény|Az Azure Portal irányítópult stílusa</br>[Riasztások e-mailben](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Az Azure AD Connect használatának licenckövetelményei

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Az Azure AD Connect Health használatának licenckövetelményei
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>További lépések

- [Hardver és előfeltételek](how-to-connect-install-prerequisites.md) 
- [Gyorsbeállítások](how-to-connect-install-express.md)
- [Testreszabott beállítások](how-to-connect-install-custom.md)
- [Az Azure AD Connect Health-ügynökök telepítése](how-to-connect-health-agent-install.md) 
