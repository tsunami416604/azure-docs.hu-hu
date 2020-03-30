---
title: Vállalati biztonsági irányelvek az Azure HDInsightban
description: Néhány gyakorlati tanácsok, amelyek megkönnyítik a vállalati biztonsági csomag telepítését és kezelését.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: be6c1fdc5deb6d541656c198469822dae0a5f7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463205"
---
# <a name="enterprise-security-general-information-and-guidelines-in-azure-hdinsight"></a>Vállalati biztonsági általános információk és irányelvek az Azure HDInsightban

Biztonságos HDInsight-fürt telepítésekor van néhány gyakorlati, amely megkönnyíti a központi telepítés és a fürtkezelés. Néhány általános információt és iránymutatást itt tárgyalunk.

## <a name="use-of-secure-cluster"></a>Biztonságos fürt használata

### <a name="recommended"></a>Ajánlott

* A fürtöt egyszerre több felhasználó fogja használni.
* A felhasználók különböző szintű hozzáféréssel rendelkeznek ugyanazokhoz az adatokhoz.

### <a name="not-necessary"></a>Nem szükséges

* Csak automatizált feladatokat (például egyszemélyes felhasználói fiókot) fog futtatni, a szabványos fürt elég jó.
* Az adatimportálást egy szabványos fürt használatával is elvégezheti, és ugyanazt a tárfiókot használhatja egy másik biztonságos fürtön, ahol a felhasználók elemzési feladatokat futtathatnak.

## <a name="use-of-local-account"></a>Helyi fiók használata

* Ha megosztott felhasználói vagy helyi fiókot használ, nehéz lesz azonosítani, hogy ki használta a fiókot a konfiguráció vagy a szolgáltatás módosítására.
* A helyi fiókok használata akkor problémás, ha a felhasználók már nem részei a szervezetnek.

## <a name="ranger"></a>Ranger

### <a name="policies"></a>Házirendek

* Alapértelmezés szerint a Ranger **a Megtagadás** házirendet használja.

* Ha az adathozzáférés olyan szolgáltatáson keresztül történik, ahol az engedélyezés engedélyezve van:
  * A ranger engedélyezési bővítményt a kérelem hivatkozik, és a kérelem kontextusában.
  * A Ranger a szolgáltatáshoz konfigurált házirendeket alkalmazza. Ha a Ranger házirendek sikertelenek, a hozzáférés-ellenőrzés a fájlrendszerre halasztódik. Egyes szolgáltatások, mint a MapReduce csak akkor ellenőrzi, ha a fájl / mappa tulajdonosa ugyanaz a felhasználó, aki benyújtja a kérelmet. Szolgáltatások, mint a Hive, ellenőrizze, hogy`rwx`a tulajdonjog egyezik-e vagy a megfelelő fájlrendszer-engedélyek ( ).

* A Hive, amellett, hogy az engedélyeket, hogy nem Létrehozása `rwx`/ Frissítése / törlése engedélyek, a felhasználónak rendelkeznie kell engedélyekkel a könyvtárban a tároló és az összes al könyvtárakat.

* A házirendek egyének helyett csoportokra (előnyösebb) alkalmazhatók.

* A Ranger authorizer minden kérésnél kiértékeli az adott szolgáltatás összes Ranger szabályzatát. Ez az értékelés hatással lehet a feladat vagy lekérdezés elfogadásához.

### <a name="storage-access"></a>Tárolási hozzáférés

* Ha a tárolási típus WASB, akkor nincs OAuth token.
* Ha a Ranger végrehajtotta az engedélyezést, majd a tárolási hozzáférés a felügyelt identitás használatával történik.
* Ha a Ranger nem végzett semmilyen hitelesítést, majd a tárolási hozzáférés a felhasználó OAuth token használatával történik.

### <a name="hierarchical-name-space"></a>Hierarchikus névtér

Ha a hierarchikus névköznincs engedélyezve:

* Nincsenek örökölt engedélyek.
* Csak a fájlrendszer engedélye, amely működik storage **data XXXX** RBAC szerepkör, hozzá kell rendelni a felhasználó közvetlenül az Azure Portalon.

### <a name="default-hdfs-permissions"></a>Alapértelmezett HDFS-engedélyek

* Alapértelmezés szerint a felhasználók nem férnek hozzá a mappához a **/** HDFS (kell a storage blob tulajdonosi szerepkör a hozzáférés sikeres eléréséhez).
* A mapreduce és mások átmeneti könyvtárához létrejön egy `sticky _wx` felhasználóspecifikus könyvtár, és engedélyeket biztosít. A felhasználók létrehozhatnak alatta fájlokat és mappákat, de más elemeket nem.

### <a name="url-auth"></a>URL-hitelesítés

Ha az url auth engedélyezve van:

* A konfiguráció tartalmazni fogja, hogy milyen előtagok szerepelnek `adl://`az url auth (például ).
* Ha a hozzáférés ehhez az url-hez van, akkor a Ranger ellenőrzi, hogy a felhasználó szerepel-e az engedélyezési listában.
* A ranger nem ellenőrzi a finom szabályokat.

## <a name="resource-groups"></a>Erőforráscsoportok

Használjon új erőforráscsoportot minden fürthöz, hogy különbséget tehessen a fürt erőforrásai között.

## <a name="nsgs-firewalls-and-internal-gateway"></a>NSG-k, tűzfalak és belső átjárók

* A virtuális hálózatok zárolásához használja a hálózati biztonsági csoportokat (NSG-k).
* A kimenő hozzáférési házirendek kezeléséhez használjon tűzfalat.
* Használja a nyilvános internetre nem nyitott belső átjárót.

## <a name="azure-active-directory"></a>Azure Active Directory

[Az Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatása.

### <a name="policies"></a>Házirendek

* Tiltsa le a feltételes hozzáférési házirendet az IP-címalapú házirend használatával. Ehhez a szolgáltatásvégpontok engedélyezéséhez kell engedélyezni azon a Virtuálisfelhasználó-állomásokon, ahol a fürtök telepítve vannak. Ha az MFA-hoz (az AAD-től eltérő) külső szolgáltatást használ, az IP-címalapú házirend nem fog működni.

* `AllowCloudPasswordValidation`az összevont felhasználók számára szükség van. Mivel a HDInsight közvetlenül használja a felhasználónevet/jelszót az Azure AD-ből származó jogkivonatok lekéréséhez, ezt a szabályzatot minden összevont felhasználó számára engedélyezni kell.

* Engedélyezze a szolgáltatásvégpontokat, ha feltételes hozzáférés-megkerülésre van szüksége a megbízható IP-k használatával.

### <a name="groups"></a>Csoportok

* A fürtöket mindig csoporton ként telepítse.
* Az Azure AD segítségével kezelheti a csoporttagságokat (egyszerűbb, mint a fürt egyes szolgáltatásait kezelni).

### <a name="user-accounts"></a>Felhasználói fiókok

* Minden forgatókönyvhöz használjon egyedi felhasználói fiókot. Például használjon egy fiókot importáláshoz, használjon egy másikat lekérdezéshez vagy más feldolgozási feladatokhoz.
* Az egyes házirendek helyett csoportalapú Ranger-házirendeket használjon.
* Tervvel kell kezelnie azokat a felhasználókat, akiknek már nem kellene fürthöz hozzáférniük.

## <a name="azure-active-directory-domain-services"></a>Azure Active Directory tartományi szolgáltatások

[Az Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/overview.md) (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint a tartományhoz való csatlakozás, a csoportházirend, a könnyű címtár-hozzáférési protokoll (LDAP) és a Kerberos / NTLM-hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryval.

Az Azure AD DS szükséges a biztonságos fürtök tartományhoz való csatlakozásához.
A HDInsight nem függhet a helyszíni tartományvezérlőktől vagy egyéni tartományvezérlőktől, mivel túl sok hibapontot, hitelesítő adatok megosztását, DNS-engedélyeket és így tovább vezet be. További információ: [Azure AD DS GYIK](../../active-directory-domain-services/faqs.md).

### <a name="azure-ad-ds-instance"></a>Azure AD DS-példány

* Hozza létre a `.onmicrosoft.com domain`példányt a segítségével. Így nem lesz több DNS-kiszolgáló, amely a tartományt szolgálja.
* Hozzon létre egy önaláírt tanúsítványt az LDAPS-hez, és töltse fel az Azure AD DS-be.
* Használjon társviszony-létesített virtuális hálózatot fürtök üzembe helyezéséhez (ha több csapat telepíti a HDInsight ESP-fürtöket, ez hasznos lesz). Ez biztosítja, hogy nem kell megnyitni a portokat (NSG-ket) a tartományvezérlővel rendelkező virtuális hálózaton.
* Konfigurálja a VIRTUÁLIS HÁLÓZAT DNS-ét megfelelően (az Azure AD DS tartománynevének állomásfájl-bejegyzések nélkül kell feloldania).
* Ha korlátozza a kimenő forgalmat, győződjön meg arról, hogy elolvasta a [tűzfal támogatását a HDInsightban](../hdinsight-restrict-outbound-traffic.md)

### <a name="properties-synced-from-azure-ad-to-azure-ad-ds"></a>Az Azure AD-ből az Azure AD DS-re szinkronizált tulajdonságok

* Az Azure AD a helyszíni szinkronizálásokat csatlakoztatja az Azure AD-hez.
* Az Azure AD DS szinkronizálja az Azure AD-ből.

Az Azure AD DS rendszeres időközönként szinkronizálja az Azure AD-ből származó objektumokat. Az Azure AD DS panel az Azure Portalon megjeleníti a szinkronizálási állapot. A szinkronizálás minden szakaszában az egyedi tulajdonságok ütközésbe kerülhetnek, és átnevezhetők. Ügyeljen arra, hogy a tulajdonság-leképezés az Azure AD az Azure AD DS.

További információ: [Azure AD UserPrincipalName population](../../active-directory/hybrid/plan-connect-userprincipalname.md), and [How Azure AD DS synchronization works.](../../active-directory-domain-services/synchronization.md)

### <a name="password-hash-sync"></a>Jelszókivonat-szinkronizálás

* A jelszavak szinkronizálása a többi objektumtípustól eltérően lesz. Csak a nem visszafordítható jelszókimondottak vannak szinkronizálva az Azure AD és az Azure AD DS szolgáltatásban
* Az Azure AD-nek a helyszínen történő használatát az AD Connect en keresztül kell engedélyezni
* Az Azure AD AD DS-szinkronizálás automatikus (késések 20 perc alatt).
* A jelszókihatárolások csak akkor lesznek szinkronizálva, ha megváltozott a jelszó. Ha engedélyezi a jelszókivonat-szinkronizálást, az összes meglévő jelszó nem szinkronizálódik automatikusan, mivel visszafordíthatatlanul tárolják őket. A jelszó módosításakor a rendszer szinkronizálja a jelszókimondottakat.

### <a name="computer-objects-location"></a>Számítógép-objektumok helye

Minden fürt egyetlen szervezeti egységhez van társítva. Egy belső felhasználó van kiépítve a szervezeti egységben. Az összes csomópont tartomány csatlakozik ugyanahhoz a szervezeti egységhez.

### <a name="active-directory-administrative-tools"></a>Active Directory felügyeleti eszközei

Az Active Directory felügyeleti eszközeinek Windows Server virtuális gépre való telepítésével kapcsolatos tudnivalókat a Felügyeleti eszközök telepítése című témakörben [tismerteti.](../../active-directory-domain-services/tutorial-create-management-vm.md)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="cluster-creation-fails-repeatedly"></a>A fürt létrehozása ismételten sikertelen

Leggyakoribb okok:

* A DNS-konfiguráció nem megfelelő, a fürtcsomópontok tartományhoz való csatlakozása sikertelen.
* Az NSG-k túl korlátozóak, így megakadályozzák a tartományhoz való csatlakozást.
* A felügyelt identitás nem rendelkezik a megfelelő engedélyekkel.
* A fürtnév nem egyedi az első hat karakternél (egy másik élő fürttel vagy egy törölt fürttel).

## <a name="next-steps"></a>További lépések

* [Vállalati biztonsági csomag konfigurációi az Azure Active Directory tartományi szolgáltatásokkal a HDInsightban](./apache-domain-joined-configure-using-azure-adds.md)

* [Az Azure Active Directory felhasználóinak szinkronizálása EGY HDInsight-fürttel.](../hdinsight-sync-aad-users-to-cluster.md)
