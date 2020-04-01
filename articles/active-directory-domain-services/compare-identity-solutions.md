---
title: Active Directory-alapú szolgáltatások összehasonlítása az Azure-ban | Microsoft dokumentumok
description: Ebben az áttekintésben összehasonlíthatja az Active Directory tartományi szolgáltatások, az Azure Active Directory és az Azure Active Directory tartományi szolgáltatások különböző identitásajánlatait.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 62826b209b37e5abe5d33455809ba1b894ddb85f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476454"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Saját felügyeletű Active Directory tartományi szolgáltatások, Azure Active Directory és felügyelt Azure Active Directory tartományi szolgáltatások összehasonlítása

Alkalmazások, szolgáltatások vagy eszközök központi identitáshoz való hozzáférésének biztosításához három gyakori módja van az Active Directory-alapú szolgáltatások azure-beli használatának. Ez a választás az identitáskezelési megoldások ban rugalmasságot biztosít a szervezet igényeinek leginkább megfelelő címtár használatához. Ha például többnyire csak felhőalapú felhasználókat kezel, amelyek mobileszközöket futtatnak, előfordulhat, hogy nincs értelme saját Active Directory tartományi szolgáltatások (AD DS) identitáskezelési megoldását felépíteni és futtatni. Ehelyett csak az Azure Active Directoryt használhatja.

Bár a három Active Directory-alapú identitásmegoldás közös névvel és technológiával rendelkezik, úgy tervezték őket, hogy olyan szolgáltatásokat nyújtsanak, amelyek megfelelnek a különböző ügyféligényeknek. Magas szinten ezek az identitásmegoldások és szolgáltatáskészletek a következők:

* **Active Directory tartományi szolgáltatások (AD DS)** – Nagyvállalati használatra kész könnyű címtár-hozzáférési protokoll (LDAP) kiszolgáló, amely olyan kulcsfontosságú szolgáltatásokat nyújt, mint az identitás és a hitelesítés, a számítógépobjektum-kezelés, a csoportházirend és a megbízhatósági kapcsolatok.
    * Az AD DS számos helyszíni informatikai környezettel rendelkező szervezet központi összetevője, és alapvető felhasználói fiókhitelesítési és számítógép-kezelési funkciókat biztosít.
* **Azure Active Directory (Azure AD)** – Felhőalapú identitás- és mobileszköz-kezelés, amely felhasználói fiókot és hitelesítési szolgáltatásokat biztosít az olyan erőforrásokhoz, mint az Office 365, az Azure Portal vagy a SaaS-alkalmazások.
    * Az Azure AD szinkronizálható egy helyszíni Active Angeles-beli DS-környezettel, hogy egyetlen identitást biztosítson a felhőben natívan működő felhasználóknak.
* **Azure Active Directory tartományi szolgáltatások (Azure AD DS)** – Felügyelt tartományi szolgáltatásokat biztosít a teljes mértékben kompatibilis hagyományos Active Directory tartományi szolgáltatások olyan szolgáltatásainak egy részhalmazával, mint a tartományhoz való csatlakozás, a csoportházirend, az LDAP és a Kerberos / NTLM-hitelesítés.
    * Az Azure AD DS integrálható az Azure AD-vel, amely maga is szinkronizálható egy helyszíni Active Angeles-beli Active Angeles-i környezettel. Ez a képesség kiterjeszti a központi identitáshasználati eseteket a hagyományos webes alkalmazásokra, amelyek az Azure-ban futnak egy lift-and-shift stratégia részeként.

Ez az áttekintő cikk összehasonlítja és összehasonlítja, hogy ezek az identitáskezelési megoldások hogyan működhetnek együtt, vagy hogyan használhatók egymástól függetlenül, a szervezet igényeitől függően.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Az Azure AD DS és a saját felügyelt AD DS

Ha olyan alkalmazásokkal és szolgáltatásokkal rendelkezik, amelyeknek hozzá kell férniük a hagyományos hitelesítési mechanizmusokhoz, például a Kerberos hoz vagy az NTLM-hez, kétféleképpen biztosíthatja az Active Directory tartományi szolgáltatásokat a felhőben:

* Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) használatával létrehozott *felügyelt* tartomány. A Microsoft létrehozza és kezeli a szükséges erőforrásokat.
* Saját *maga által felügyelt* tartomány, amelyet hagyományos erőforrásokkal, például virtuális gépekkel ,például virtuális gépekkel, Windows Server vendég operációs rendszerrel és Active Directory tartományi szolgáltatásokkal (AD DS) hoz létre és konfigurál. Ezután továbbra is felügyelheti ezeket az erőforrásokat.

Az Azure AD DS segítségével az alapvető szolgáltatásösszetevőket a Microsoft *felügyelt* tartományként telepíti és karbantartja. Nem telepíti, kezelheti, nem javíthatja és nem biztonságossá teszi az AD DS-infrastruktúrát olyan összetevők számára, mint a virtuális gépek, a Windows Server operációs rendszer vagy a tartományvezérlők.

Az Azure AD DS a hagyományos, saját kezelésű AD DS-környezet funkcióinak kisebb részét biztosítja, ami csökkenti a tervezés és a felügyelet összetettségét. Például nincsenek AD-erdők, tartományok, helyek és replikációs hivatkozások a tervezéshez és karbantartáshoz. A felhőben futó alkalmazások és szolgáltatások esetében, amelyeknek hozzáférésre van szükségük a hagyományos hitelesítési mechanizmusokhoz, például a Kerberoshoz vagy az NTLM-hez, az Azure AD DS felügyelt tartományi élményt nyújt a minimális felügyeleti többletterheléssel.

Ha önkezelt Active Directory tartományi szolgáltatások környezetét telepíti és futtatja, az összes kapcsolódó infrastruktúrát és címtár-összetevőt karban kell tartania. Az önfelügyelt AD DS-környezet további karbantartási többletterhelést jelent, de ezután további feladatokat is eltud végezni, például kiterjesztheti a sémát, vagy erdőszintű megbízhatósági kapcsolatokat hozhat létre.

A felhőben lévő alkalmazások és szolgáltatások identitását kiszolgáló, saját felügyelet ű AD DS-környezet gyakori telepítési modelljei a következők:

* **Önálló, csak felhőalapú AD DS** – Az Azure-beli virtuális gépek tartományvezérlőként vannak konfigurálva, és egy külön, csak felhőalapú AD DS-környezet jön létre. Ez az AD DS-környezet nem integrálható a helyszíni AD DS-környezettel. A hitelesítő adatok egy másik készletét használják a bejelentkezéshez és a virtuális gépek felügyeletéhez a felhőben.
* **Erőforrás-erdő telepítése** – Az Azure virtuális gépek tartományvezérlőként vannak konfigurálva, és létrejön egy meglévő erdő részét vevő AD DS-tartomány. A megbízhatósági kapcsolat ezután egy helyszíni AD DS-környezetbe van konfigurálva. Más Azure-virtuális gépek tartomány-csatlakozás az erőforrás-erdő a felhőben. A felhasználói hitelesítés VPN/ ExpressRoute-kapcsolaton keresztül fut a helyszíni AD DS-környezettel.
* **A helyszíni tartomány kiterjesztése az Azure-ra** – Egy Azure-beli virtuális hálózat VPN/ExpressRoute-kapcsolaton keresztül csatlakozik egy helyszíni hálózathoz. Az Azure virtuális gépek ehhez az Azure virtuális hálózathoz csatlakoznak, amely lehetővé teszi számukra, hogy tartományhoz csatlakozzanak a helyszíni AD DS-környezethez.
    * Egy másik lehetőség az Azure virtuális gépek létrehozása, és előlépteti őket replika tartományvezérlők a helyszíni AD DS-tartományból. Ezek a tartományvezérlők VPN/ ExpressRoute-kapcsolaton keresztül replikálnak a helyszíni AD DS-környezetbe. A helyszíni AD DS-tartomány hatékonyan ki van terjesztve az Azure-ba.

Az alábbi táblázat ismerteti a szervezet számára szükséges funkciók at, valamint a felügyelt Azure AD DS-tartomány vagy a saját felügyelt AD DS-tartomány közötti különbségeket:

| **Szolgáltatás** | **Azure AD DS** | **Saját irányítású AD DS** |
| ----------- |:---------------:|:----------------------:|
| **Felügyelt szolgáltatás**                               | **&#x2713;** | **&#x2715;** |
| **Biztonságos telepítések**                            | **&#x2713;** | A rendszergazda biztonságossá teszi a telepítést |
| **DNS-kiszolgáló**                                    | **&#x2713;** (felügyelt szolgáltatás) |**&#x2713;** |
| **Tartományi vagy vállalati rendszergazdai jogosultságok** | **&#x2715;** | **&#x2713;** |
| **Csatlakozás tartományhoz**                                   | **&#x2713;** | **&#x2713;** |
| **Tartományhitelesítés NTLM és Kerberos használatával** | **&#x2713;** | **&#x2713;** |
| **Kerberos korlátozott delegálás**               | Erőforrás-alapú | Erőforrás-alapú & számlaalapú|
| **Egyéni szervezeti egység szerkezete**                           | **&#x2713;** | **&#x2713;** |
| **Csoportházirend**                                  | **&#x2713;** | **&#x2713;** |
| **Sémabővítmények**                             | **&#x2715;** | **&#x2713;** |
| **AD tartomány / erdő szintű bizalmi kapcsolatok**                     | **&#x2713;** (csak egyirányú kimenő erdőszintű bizalmi kapcsolatok) | **&#x2713;** |
| **Biztonságos LDAP (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP olvasása**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP írása**                                    | **&#x2713;** (a felügyelt tartományon belül) | **&#x2713;** |
| **Földrajzilag elosztott telepítések**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Az Azure AD DS és az Azure AD

Az Azure AD lehetővé teszi a szervezet által használt eszközök identitásának kezelését és a vállalati erőforrásokhoz való hozzáférést ezekből az eszközökről. A felhasználók is regisztrálhatják a személyes eszköz (a bring-your-own (BYO) modell) az Azure AD, amely biztosítja az eszköz identitást. Az Azure AD ezután hitelesíti az eszközt, amikor egy felhasználó bejelentkezik az Azure AD-be, és az eszközt használja a biztonságos erőforrások eléréséhez. Az eszköz a Mobileszköz-kezelés (MDM) szoftverrel, például a Microsoft Intune-nal kezelhető. Ez a felügyeleti képesség lehetővé teszi, hogy a bizalmas erőforrásokhoz való hozzáférést a felügyelt és a házirend-kompatibilis eszközökre korlátozza.

A hagyományos számítógépek és laptopok is csatlakozhatnak az Azure AD-hez. Ez a mechanizmus ugyanazokat az előnyöket kínálja a személyes eszköz regisztrálása az Azure AD-vel, például lehetővé teszi a felhasználók számára, hogy jelentkezzen be az eszközre a vállalati hitelesítő adatok használatával.

Az Azure AD-hez csatlakozott eszközök a következő előnyökkel járnak:

* Egyszeri bejelentkezés (SSO) az Azure AD által védett alkalmazásokhoz.
* A felhasználói beállítások vállalati házirend-kompatibilis barangolása az eszközök között.
* Hozzáférés a Windows Áruház vállalati hitelesítő adataival.
* Windows Hello for Business.
* Korlátozott hozzáférés az alkalmazásokhoz és az erőforrásokhoz a vállalati szabályzatnak megfelelő eszközökről.

Az eszközök csatlakoztathatók az Azure AD-hez egy olyan hibrid telepítéssel vagy anélkül, amely helyszíni AD DS-környezetet is tartalmaz. Az alábbi táblázat ismerteti az általános eszköztulajdonosi modelleket, valamint azt, hogy általában hogyan csatlakoznak egy tartományhoz:

| **Az eszköz típusa**                                        | **Eszközplatformok**             | **Mechanizmus**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Személyes eszközök                                          | Windows 10, iOS, Android, Mac OS | Az Azure AD-ban regisztrálva    |
| A szervezet tulajdonában lévő eszköz nem csatlakozik a helyszíni AD DS-hez | Windows 10                       | Azure AD-hez csatlakoztatva        |
| Szervezet tulajdonában lévő eszköz egy helyszíni AD DS-hez csatlakoztatva  | Windows 10                       | csatlakozik a Hibrid Azure AD-hez |

Egy Azure AD-hez vagy regisztrált eszközön a felhasználói hitelesítés a modern OAuth / OpenID Connect alapú protokollok használatával történik. Ezeket a protokollokat úgy tervezték, hogy az interneten keresztül működjenek, így nagyszerűek olyan mobil helyzetekben, ahol a felhasználók bárhonnan hozzáférhetnek a vállalati erőforrásokhoz.

Az Azure AD DS-hez összekapcsolt eszközökkel az alkalmazások használhatják a Kerberos és az NTLM protokollokat a hitelesítéshez, így támogathatják az Azure virtuális gépeken egy lift-and-shift stratégia részeként áttelepített örökölt alkalmazásokat. Az alábbi táblázat az eszközök ábrázolásának módjában mutatkozó különbségeket ismerteti, és hitelesítheti magát a címtárban:

| **Aspektus**                      | **Az Azure AD-hez csatlakozott**                                 | **Az Azure AD DS-hez csatlakoztatva**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| A készülék által vezérelt            | Azure AD                                            | Azure AD DS felügyelt tartomány                                                |
| Képviselet a címtárban | Eszközobjektumok az Azure AD-címtárban            | Számítógép-objektumok az Azure AD DS felügyelt tartományában                        |
| Hitelesítés                  | OAuth / OpenID Connect alapú protokollok              | Kerberos és NTLM protokollok                                               |
| Kezelés                      | Mobileszköz-felügyeleti (MDM) szoftver, például az Intune | Csoportházirend                                                              |
| Hálózat                      | Működik az interneten keresztül                             | A felügyelt tartományt kiszolgáló virtuális hálózathoz kell csatlakoznia, vagy társviszonyba kell lépnie vele. |
| Nagyszerű...                    | Végfelhasználói mobil- vagy asztali eszközök                  | Az Azure-ban telepített kiszolgálói virtuális gépek                                              |

## <a name="next-steps"></a>További lépések

Az Azure AD DS használatának megkezdéséhez [hozzon létre egy Azure AD DS felügyelt tartományt az Azure Portalon.][tutorial-create]

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
