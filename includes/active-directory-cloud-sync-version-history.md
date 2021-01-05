---
ms.openlocfilehash: 77866fcb72515d5167d9f06b7d9ad91c59bc7945
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2020
ms.locfileid: "97700595"
---
Ez a cikk a kiadott Azure Active Directory Connect kiépítési ügynök verzióit és funkcióit sorolja fel. Az Azure AD csapata rendszeresen frissíti a kiépítési ügynököt új funkciókkal és funkciókkal. A kiépítési ügynök automatikusan frissül, amikor megjelent egy új verzió. 

A Microsoft közvetlen támogatást biztosít az ügynök legújabb verziójához és egy korábbi verzióhoz.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Kiadás állapota

November 23., 2020: kiadva a letöltéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* A [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts) támogatása
* A növekményes és a különbözeti szinkronizálási ciklus során a 1500-nál kevesebb taggal rendelkező csoportok támogatása. Ez a csoport-hatókörű szűrő használatakor alkalmazható
* Nagyméretű csoportok támogatása a tagok méretével akár 15 000 fordulat
* A szinkronizálás kezdeti fejlesztése
* Részletes részletes naplózás
* A [AADCloudSyncTools PowerShell-modul](../articles/active-directory/cloud-provisioning/reference-powershell.md) bemutatása
* Rögzített korlátozások az ügynök nem angol nyelvű kiszolgálóra való telepítésének engedélyezéséhez
* Csak a hatókörben lévő objektumok PHS-szűrésének támogatása (eredetileg a jelszó-kivonatok szinkronizálása minden objektum esetében)
* A memóriavesztés hibájának kijavítása az ügynökben
* Továbbfejlesztett kiépítési naplók
* Az LDAP- [kapcsolat időtúllépésének](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-ldap-connection-timeout) konfigurálásának támogatása 
* Az [átirányítási](../articles/active-directory/cloud-provisioning/how-to-manage-registry-options.md#configure-referral-chasing) beállítások konfigurálásának támogatása 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Kiadás állapota

2019. december 4., letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* A [Azure ad Connect felhőalapú](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) kiépítésének támogatásával szinkronizálhatja a felhasználókat, a kapcsolattartókat és a csoport adatait a helyszíni Active Directoryból az Azure ad-be


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Kiadás állapota

2019. szeptember 9.: kiadva az automatikus frissítéshez

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* További nyomkövetési és naplózási beállítások konfigurálása a kiépítési ügynökkel kapcsolatos problémák felderítéséhez
* Csak azokat az Azure AD-attribútumokat lehet beolvasni, amelyek a leképezésben vannak konfigurálva a szinkronizálás teljesítményének növeléséhez

### <a name="fixed-issues"></a>Megoldott problémák

* Kijavítva egy olyan hibát, amelyben az ügynök nem válaszoló állapotba került, ha probléma merült fel az Azure AD-beli kapcsolatok meghibásodása esetén
* Kijavított egy hibát, amely problémát okozott, amikor a bináris adatok beolvasása Azure Active Directory
* Kijavítva egy olyan hibát, amelyben az ügynök nem tudta megújítani a bizalmi kapcsolatot a Cloud Hybrid Identity Service használatával

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Kiadás állapota

2019. január 23., letöltésre kiadva

### <a name="new-features-and-improvements"></a>Új funkciók és Újdonságok

* Kitatarozta a kiépítési ügynököt és az összekötő architektúráját a jobb teljesítmény, stabilitás és megbízhatóság érdekében 
* A kiépítési ügynök konfigurációjának egyszerűsítése a felhasználói felületen alapuló telepítővarázsló használatával 
* Az automatikus ügynök frissítéseinek támogatása


