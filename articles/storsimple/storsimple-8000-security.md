---
title: StorSimple 8000 sorozatú biztonság | Microsoft dokumentumok
description: A StorSimple szolgáltatás, az eszköz és az adatok helyszíni és felhőbeli védelmét szolgáló biztonsági és adatvédelmi funkciók ismertetése.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 4598f71f9b611e68f8eb00676138784833c39f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891515"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple biztonság és adatvédelem

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés

A biztonság komoly aggodalomra ad okot mindenki számára, aki új technológiát alkalmaz, különösen akkor, ha a technológiát bizalmas vagy védett adatokkal használják. A különböző technológiák értékelése közben figyelembe kell vennie a megnövekedett adatvédelmi kockázatokat és költségeket. A Microsoft Azure StorSimple biztonsági és adatvédelmi megoldást is kínál az adatvédelemhez, így biztosítva:

* **Bizalmas kezelés** – Csak a jogosult entitások tekinthetik meg az adatokat.
* **Integritás** – Csak a jogosult entitások módosíthatják vagy törölhetik az adatokat.

A Microsoft Azure StorSimple megoldás négy fő összetevőből áll, amelyek kölcsönhatásba lépnek egymással:

* **A Microsoft Azure-ban üzemeltetett StorSimple Eszközkezelő szolgáltatás** – A StorSimple-eszköz konfigurálásához és kiépítéséhez használt felügyeleti szolgáltatás.
* **StorSimple eszköz** – Az adatközpontban telepített fizikai eszköz. Az adatokat generáló összes állomás és ügyfél csatlakozik a StorSimple-eszközhöz, és az eszköz kezeli az adatokat, és szükség szerint áthelyezi azokat az Azure-felhőbe.
* **Az eszközhöz csatlakoztatott ügyfelek/állomások** – Az infrastruktúra azon ügyfelei, amelyek a StorSimple-eszközhöz csatlakoznak, és védett adatokat hoznak létre.
* **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik.

A következő szakaszok ismertetik a StorSimple biztonsági funkciók, amelyek segítenek megvédeni az egyes összetevők és a rajtuk tárolt adatok. Emellett tartalmazza a Microsoft Azure StorSimple biztonságával kapcsolatos kérdések listáját és a megfelelő válaszokat.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Eszközkezelő szolgáltatás védelme

A StorSimple Eszközkezelő szolgáltatás a Microsoft Azure-ban üzemeltetett felügyeleti szolgáltatás, amely a szervezet által beszerzett összes StorSimple-eszköz kezelésére szolgál. A StorSimple Eszközkezelő szolgáltatás a szervezeti hitelesítő adatok segítségével egy webböngészőn keresztül jelentkezhet be az Azure Portalra.

A StorSimple Eszközkezelő szolgáltatás hoz való hozzáférés hez a szervezetnek rendelkeznie kell egy Azure-előfizetéssel, amely tartalmazza a StorSimple szolgáltatást. Az előfizetés szabályozza, hogy az Azure Portal mely szolgáltatásai érhetők el. Ha a szervezet nem rendelkezik Azure-előfizetéssel, és szeretne többet megtudni róluk, olvassa el [a Regisztráció az Azure-ért szervezetként](../active-directory/fundamentals/sign-up-organization.md)című témakört.

Mivel a StorSimple Eszközkezelő szolgáltatás az Azure-ban található, az Azure biztonsági funkciói védik. A Microsoft Azure által biztosított biztonsági funkciókról a [Microsoft Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/security/) talál további információt.

## <a name="storsimple-device-protection"></a>StorSimple eszközvédelem

A StorSimple eszköz egy helyszíni hibrid tárolóeszköz, amely ssd-meghajtókat (SSD- ket) és merevlemez-meghajtókat (HDD-ket) tartalmaz, redundáns vezérlőkkel és automatikus feladatátvételi képességekkel együtt. A vezérlők kezelik a tárolási rétegezést, a jelenleg használt (vagy gyakori elérésű) adatokat a helyi tárolóban (a StorSimple-eszközön vagy a helyszíni kiszolgálókon), miközben a ritkábban használt adatokat a felhőbe helyezik át.

Csak az engedélyezett StorSimple-eszközök csatlakozhatnak az Azure-előfizetésében létrehozott StorSimple Eszközkezelő szolgáltatáshoz. Egy eszköz engedélyezéséhez regisztrálnia kell azt a StorSimple Eszközkezelő szolgáltatásban a szolgáltatás regisztrációs kulcsának biztosításával. A szolgáltatás regisztrációs kulcs egy 128 bites véletlenszerű kulcs az Azure Portalon létrehozott.

![Szolgáltatás regisztrációs kulcsa](./media/storsimple-security/ServiceRegistrationKey.png)

A szolgáltatásregisztrációs kulcs beszerezésének módjáról a [2.](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)

A szolgáltatás regisztrációs kulcsa egy hosszú kulcs, amely több mint 100 karaktert tartalmaz. A kulcsot átmásolhatja és egy biztonságos helyre lévő szövegfájlba mentheti, így szükség esetén további eszközöket is engedélyezhet. Ha a szolgáltatás regisztrációs kulcsa elveszik az első eszköz regisztrálása után, új kulcsot hozhat létre a StorSimple Eszközkezelő szolgáltatásból. Ez nem befolyásolja a meglévő eszközök működését.

Az eszköz regisztrálása után jogkivonatokat használ a Microsoft Azure-ral való kommunikációhoz. A szolgáltatás regisztrációs kulcsa nem használatos az eszköz regisztrációja után.

> [!NOTE]
> Azt javasoljuk, hogy minden használat után hozza létre újra a szolgáltatás regisztrációs kulcsát.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Védje StorSimple megoldását jelszavakkal

A jelszavak a számítógép biztonságának fontos szempontjai, és széles körben használják a StorSimple megoldásban annak érdekében, hogy az adatok csak a jogosult felhasználók számára legyenek elérhetők. A StorSimple lehetővé teszi a következő jelszavak konfigurálását:

* StorSimple eszközrendszergazdai jelszó
* Kihívás kézfogás hitelesítési protokoll (CHAP) kezdeményezője és a céljelszavak
* StorSimple Snapshot Manager jelszava

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell a StorSimple-hez és a StorSimple eszköz rendszergazdai jelszava

A Windows PowerShell for StorSimple egy parancssori felület, amely a StorSimple eszköz kezeléséhez használható. A Windows PowerShell for StorSimple olyan funkciókkal rendelkezik, amelyek lehetővé teszik az eszköz regisztrálását, a hálózati felület konfigurálását az eszközön, bizonyos típusú frissítések telepítését, az eszköz hibaelhárítását a támogatási munkamenet elérésével, és az eszköz állapotának módosítását. A Windows PowerShell for StorSimple az eszközön lévő soros konzolhoz való csatlakozással vagy a Windows PowerShell-táv-üzenet teljhatalmú használatával érhető el.

A PowerShell-távhívás HTTPS-en vagy HTTP-n keresztül is elvégezhető. Ha a HTTPS protokollon keresztüli távoli felügyelet engedélyezve van, le kell töltenie a távoli felügyeleti tanúsítványt az eszközről, és telepítenie kell a távoli ügyfélre. A PowerShell-távirányítással kapcsolatos további információkért látogasson el a [Csatlakozás távolról a StorSimple-eszközhöz című webhelyre.](storsimple-8000-remote-connect.md)

Miután a Windows PowerShell for StorSimple az eszközhöz való csatlakozáshoz, meg kell adnia az eszköz rendszergazdai jelszavát az eszközre való bejelentkezéshez.

![Az eszköz rendszergazdai jelszava](./media/storsimple-security/DeviceAdminPW.png)

Tartsa szem előtt az alábbi gyakorlati tanácsokat:

* A távkezelés alapértelmezés szerint ki van kapcsolva. A StorSimple Eszközkezelő szolgáltatás sal engedélyezheti azt. Biztonsági ajánlott eljárásként a távelérést csak abban az időszakban szabad engedélyezni, amikor az ténylegesen szükség van rá.
* Ha módosítja a jelszót, mindenképpen értesítse az összes távelérésű felhasználót, hogy ne tapasztaljanak váratlan kapcsolódási veszteséget.
* A StorSimple Eszközkezelő szolgáltatás nem tudja beolvasni a meglévő jelszavakat: csak alaphelyzetbe állíthatja őket. Javasoljuk, hogy az összes jelszót biztonságos helyen tárolja, hogy ne kelljen visszaállítania a jelszót, ha elfelejti. Ha alaphelyzetbe kell állítania a jelszót, az alaphelyzetbe állítás előtt mindenképpen értesítse az összes felhasználót.

A Windows PowerShell-felület et az eszközhöz való soros kapcsolat on keresztül érheti el. A http vagy a HTTPS használatával is távolról is elérheti, ami további biztonságot nyújt. A HTTPS nagyobb biztonságot nyújt, mint a soros vagy http-kapcsolat. A HTTPS használatához azonban először telepítenie kell egy tanúsítványt az eszközhöz hozzáférő ügyfélszámítógépre. A távelérési tanúsítvány letölthető a StorSimple Eszközkezelő szolgáltatás eszközkonfigurációs lapjáról. Ha a távelérésre vonatkozó tanúsítvány elvész, le kell töltenie egy új tanúsítványt, és továbbítania kell minden olyan ügyfélnek, amely jogosult a távkezelés használatára.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Kihívás kézfogás hitelesítési protokoll (CHAP) kezdeményezője és a céljelszavak

A CHAP egy hitelesítési séma, amelyet a StorSimple eszköz használ a távoli ügyfelek identitásának ellenőrzésére. Az ellenőrzés megosztott jelszón alapul. A CHAP lehet egyirányú (egyirányú) vagy kölcsönös (kétirányú). Az egyirányú CHAP használatával a cél (a StorSimple eszköz) hitelesíti a kezdeményező (állomás). A kölcsönös vagy fordított CHAP megköveteli, hogy a cél hitelesítse a kezdeményezőt, majd a kezdeményező hitelesítse a célt. A StorSimple konfigurálható mindkét módszer használatára.

A CHAP konfigurálásakor vegye figyelembe az alábbiakat:

* A CHAP-felhasználónév nek 233 karakternél rövidebbnek kell lennie.
* A CHAP-jelszónak 12 és 16 karakter között kell lennie. Ha hosszabb felhasználónevet vagy jelszót próbál használni, az hitelesítési hibát eredményez a Windows gazdagépen.
* Nem használhatja ugyanazt a jelszót mind a CHAP kezdeményező, mind a CHAP-cél esetében.
* A jelszó beállítása után módosítható, de nem olvasható be. Ha a jelszó módosul, győződjön meg róla, hogy értesíti az összes távelérésű felhasználók számára, hogy azok sikeresen csatlakozhatnak a StorSimple eszközhöz.

A CHAP szolgáltatással és a StorSimple-megoldáshoz való konfigurálásával kapcsolatos további információkért látogasson el a [Chap konfigurálása a StorSimple eszközhöz](storsimple-8000-configure-chap.md)című webhelyre című.

### <a name="storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager jelszava

A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amely kötetcsoportokat és a Windows kötet árnyékmásolata szolgáltatást használ alkalmazáskonzisztens biztonsági mentések létrehozásához. Emellett a StorSimple Snapshot Manager segítségével biztonsági mentési ütemezéseket hozhat létre, és klónozhat vagy visszaállíthat köteteket.

Ha konfigurálegy eszközt a StorSimple Snapshot Manager használatára, meg kell adnia a StorSimple Snapshot Manager jelszavát. Ez a jelszó először a StorSimple-hez létrehozott Windows PowerShellben van beállítva a regisztráció során. A jelszó a StorSimple Eszközkezelő szolgáltatásból is beállítható és módosítható. Ez a jelszó hitelesíti az eszközt a StorSimple Snapshot Manager segítségével.

![StorSimple Snapshot Manager jelszava](./media/storsimple-security/SnapshotMgrPassword.png)

A StorSimple Snapshot Manager jelszavának 14 és 15 karakter között kell lennie, és legalább 3 nagybetűs, kis- és numerikus és speciális karakterkombinációját kell tartalmaznia. A StorSimple Snapshot Manager jelszó beállítása után módosítható, de nem olvasható be. Ha módosítja a jelszót, mindenképpen értesítse az összes távoli felhasználót.

A StorSimple Snapshot Managerről további információt a [Mi a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Ajánlott eljárások a jelszavakhoz

Javasoljuk, hogy az alábbi irányelveket használja annak érdekében, hogy a StorSimple jelszavak erősek és jól védettek legyenek:

* Változtassa meg a jelszavát háromhavonta. A jelszavak módosítása évente érvényesül.
* Használjon erős jelszavakat. További információért nyissa meg [az Erősebb jelszavak létrehozása és védelme című](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/)lehetőséget.
* Mindig használjon különböző jelszavakat a különböző hozzáférési mechanizmusokhoz; a megadott jelszavak mindegyikének egyedinek kell lennie.
* Ne ossza meg a jelszavakat senkivel, aki nem jogosult a StorSimple eszköz elérésére.
* Ne beszéljen mások előtt a jelszóról, és ne utaljon a jelszó formátumára.
* Ha arra gyanakszik, hogy egy fiók vagy jelszó feltörték, jelentse az esetet az információbiztonsági osztálynak.
* Kezelje az összes jelszót bizalmas, bizalmas információként. 

## <a name="storsimple-data-protection"></a>StorSimple adatvédelem

Ez a szakasz a StorSimple biztonsági funkciókat ismerteti, amelyek védik az adatokat az átvitel során és a tárolt adatok.

Más szakaszokban leírtak szerint a jelszavak a felhasználók engedélyezésére és hitelesítésére szolgálnak, mielőtt hozzáférhetnének a StorSimple-megoldáshoz. Egy másik biztonsági szempont az adatok védelme a jogosulatlan felhasználóktól, miközben azokat a tárolórendszerek között és a tárolás során továbbítják. A következő szakaszok a StorSimple adatvédelmi funkcióit ismertetik.

> [!NOTE]
> A deduplikáció további védelmet nyújt a StorSimple eszközön és a Microsoft Azure-tárolóban tárolt adatok számára. Az adatok deduplikálása esetén az adatobjektumok at a leképezésükhöz és eléréséhez használt metaadatoktól elkülönítve tárolja a rendszer: nincs elérhető tárolószintű környezet az adatok kötetszerkezet, fájlrendszer vagy fájlnév alapján történő rekonstruálásához.


## <a name="protect-data-flowing-through-the-service"></a>A szolgáltatáson keresztül áramló adatok védelme

A StorSimple Eszközkezelő szolgáltatás elsődleges célja a StorSimple-eszköz kezelése és konfigurálása. A StorSimple Eszközkezelő szolgáltatás a Microsoft Azure-ban fut. Az Azure Portal segítségével adhatja meg az eszköz konfigurációs adatait, majd a Microsoft Azure a StorSimple Eszközkezelő szolgáltatás segítségével küldi el az adatokat az eszközre. A StorSimple aszimmetrikus kulcspárok rendszerét használja annak biztosítására, hogy az Azure-szolgáltatás biztonságának sérülése ne eredményezze a tárolt adatok sérülését.

![Adattitkosítás repülés közben](./media/storsimple-security/DataEncryption.png)

Az aszimmetrikus kulcsrendszer az alábbiak szerint segíti a szolgáltatáson áthaladó adatok védelmét:

1. Az aszimmetrikus nyilvános és személyes kulcspárt használó adattitkosítási tanúsítvány jön létre az eszközön, és az adatok védelmére szolgál. A kulcsok az első eszköz regisztrálásakor jönnek létre.
2. Az adattitkosítási tanúsítványkulcsok at egy személyes adatcsere (.pfx) fájlba exportálják, amelyet a szolgáltatás adattitkosítási kulcsa véd, amely egy erős 128 bites kulcs, amelyet az első eszköz véletlenszerűen generál a regisztráció során.
3. A tanúsítvány nyilvános kulcsa biztonságosan elérhető a StorSimple Eszközkezelő szolgáltatás számára, és a személyes kulcs az eszközön marad.
4. A szolgáltatásba belépő adatok titkosítva vannak a nyilvános kulcs használatával, és az eszközön tárolt személyes kulcs használatával visszafejtik, biztosítva, hogy az Azure-szolgáltatás ne tudja visszafejteni az eszközre áramló adatokat.

A szolgáltatás adattitkosítási kulcs a szolgáltatáshoz regisztrált első eszközön jön létre. A szolgáltatásban regisztrált összes további eszköznek ugyanazt a szolgáltatásadat-titkosítási kulcsot kell használnia.

> [!IMPORTANT]
> Nagyon fontos, hogy másolatot készítsen a szolgáltatás adattitkosítási kulcsáról, és biztonságos helyre mentse. A szolgáltatásadat-titkosítási kulcs egy példányát úgy kell tárolni, hogy azt egy jogosult személy elérhesse, és könnyen kommunikálhassa az eszköz rendszergazdájával.
> 
> Ha a szolgáltatás adattitkosítási kulcsa elvész, a Microsoft támogatási munkatársa segíthet a beolvasásában, feltéve, hogy legalább egy eszköz online állapotban van. Azt javasoljuk, hogy a szolgáltatás adattitkosítási kulcsának módosítása a lekérés után.

A szolgáltatásadat-titkosítási kulcs és a megfelelő adattitkosítási tanúsítvány módosításához kövesse [a StorSimple Eszközkezelő szolgáltatás titkosítási kulcsának módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)című lépéseit. A titkosítási kulcsok módosításához minden eszközt frissíteni kell az új kulccsal. Ezért azt javasoljuk, hogy módosítsa a kulcsot, ha minden eszköz online állapotban van. Ha az eszközök offline állapotban vannak, a kulcsaik más időpontban módosíthatók. Az elavult kulcsokkal rendelkező eszközök továbbra is futtathatnak biztonsági másolatokat, de a kulcs frissítéséig nem tudják visszaállítani az adatokat.

A szolgáltatás adattitkosítási kulcsa és az adattitkosítási tanúsítvány nem jár le. Azonban azt javasoljuk, hogy módosítsa a szolgáltatás adattitkosítási kulcs évente a kulcs sérülésének megelőzése érdekében.

## <a name="protect-data-at-rest"></a>Az inaktív adatok védelme

A StorSimple eszköz úgy kezeli az adatokat, hogy a használat gyakoriságától függően helyi legrétegekben és a felhőben tárolja az adatokat. Az eszközhöz csatlakoztatott összes gazdagép adatokat küld az eszközre, amelyek adott esetben áthelyezik az adatokat a felhőbe. Az adatok biztonságosan továbbítódnak az eszközről a felhőbe az interneten keresztül. Minden eszköz rendelkezik egy iSCSI-tárolóval, amely az eszközön lévő összes megosztott kötetet megírja. Minden adat titkosítva van, mielőtt a felhőbe kerül. 

![Felhőalapú tárolási titkosítási kulcs](./media/storsimple-security/CloudStorageEncryption.png)

A felhőbe áthelyezett adatok biztonságának és integritásának biztosítása érdekében a StorSimple lehetővé teszi a felhőalapú tárolási titkosítási kulcsok meghatározását az alábbiak szerint:

* Kötettároló létrehozásakor adja meg a felhőalapú tárolási titkosítási kulcsot. A kulcs nem módosítható és nem adható hozzá később.
* A kötettárolóban lévő összes kötet azonos titkosítási kulccsal rendelkezik. Ha egy adott kötethez más titkosítási formát szeretne, javasoljuk, hogy hozzon létre egy új kötettárolót a kötet üzemeltetéséhez.
* Amikor belép a felhőalapú tárolási titkosítási kulcs a StorSimple Eszközkezelő szolgáltatás, a kulcs titkosítva a szolgáltatás adattitkosítási kulcs nyilvános része, majd elküldi az eszközre.
* A felhőalapú tárolási titkosítási kulcs nem tárolja sehol a szolgáltatásban, és csak az eszköz által ismert.
* A felhőbeli tárolási titkosítási kulcs megadása nem kötelező. Az állomáson titkosított adatokat elküldheti az eszköznek.

### <a name="additional-security-best-practices"></a>További biztonsági gyakorlati tanácsok

* Osztott forgalom: elkülönítheti az iSCSI SAN-t a vállalati helyi hálózaton lévő felhasználói forgalomtól egy teljesen elkülönített hálózat telepítésével és vlan-ok használatával, ahol a fizikai elkülönítés nem lehetséges. Az iSCSI-tárolók hoz létreegy dedikált hálózat garantálja az üzleti szempontból kritikus fontosságú adatok biztonságát és teljesítményét. A tárolási és felhasználói forgalom vállalati helyi hálózaton keresztüli keverése nem ajánlott, és növelheti a késést, és hálózati hibákat okozhat.
* A gazdaoldali hálózati biztonság érdekében használjon tcp/IP kiszervezési motort (TOE) támogató hálózati adaptereket. A TOE csökkenti a PROCESSZOR TERHELÉSét a hálózati adapter TCP-jének feldolgozásával.

## <a name="protect-data-via-storage-accounts"></a>Adatok védelme tárfiókokon keresztül

Minden Egyes Microsoft Azure-előfizetés létrehozhat egy vagy több tárfiókot. (A tárfiók egyedi névteret biztosít az Azure-felhőben tárolt adatokkal való munkához.) A tárfiókhoz való hozzáférést az adott tárfiókhoz társított előfizetési és hozzáférési kulcsok szabályozzák.

Amikor létrehoz egy tárfiókot, a Microsoft Azure két 512 bites tároló-hozzáférési kulcsot hoz létre, amelyek közül az egyik hitelesítésre szolgál, amikor a StorSimple-eszköz hozzáfér a tárfiókhoz. Ne feledje, hogy csak az egyik ilyen kulcs van használatban. A másik kulcs tartalékban van, így rendszeresen elforgathatja a kulcsokat. A kulcsok elforgatásához tegye aktívvá a másodlagos kulcsot, majd törölje az elsődleges kulcsot. Ezután létrehozhat egy új kulcsot a következő forgatás során. (Biztonsági okokból sok adatközpont kulcselforgatást igényel.)

Javasoljuk, hogy kövesse az alábbi gyakorlati tanácsokat a kulcselforgatáshoz:

* Rendszeresen forgassa el a tárfiók kulcsait, hogy a tárfiókot ne férhessenek hozzá illetéktelen felhasználók.
* Rendszeres en az Azure-rendszergazda módosítania kell, vagy újra generálni az elsődleges vagy másodlagos kulcs az Azure Portal Storage szakaszhasználatával közvetlenül a tárfiók eléréséhez.

## <a name="protect-data-via-encryption"></a>Adatok védelme titkosítással

A StorSimple a következő titkosítási algoritmusokat használja a StorSimple-megoldás összetevőiben tárolt vagy között ük lévő adatok védelmére.

| Algoritmus | A kulcs hossza | Protokollok/alkalmazások/megjegyzések |
| --- | --- | --- |
| RSA |2048 |Az RSA PKCS 1 1.5-ös webhelyet az Azure Portal az eszközre küldött konfigurációs adatok titkosítására használja: például a tárfiók hitelesítő adatait, a StorSimple-eszköz konfigurációját és a felhőalapú tárolási titkosítási kulcsokat. |
| AES |256 |AES CBC használatával a szolgáltatás adattitkosítási kulcs nyilvános részének titkosítására szolgál, mielőtt az a StorSimple-eszközről az Azure Portalra kerülne. A StorSimple-eszköz is használja az adatok titkosítására, mielőtt az adatokat a felhőalapú tárfiókba küldi. |

## <a name="storsimple-cloud-appliance-security"></a>A StorSimple cloud appliance biztonsága

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Személyes adatok kezelése

A fizikai és virtuális sorozatok StorSimple eszközkezelője a következő kulcspéldányain gyűjti a személyes adatokat:

- Riasztás a felhasználói beállításokban, ahol a felhasználók e-mail címe van konfigurálva. Ezeket az információkat a rendszergazda megtekintheti és törölheti. Ez a StorSimple 8000 sorozatú eszközökre és a StorSimple virtuális tömbökre egyaránt vonatkozik.
  * A StorSimple 8000 sorozat beállításainak megtekintéséhez és törléséhez kövesse a [StorSimple-riasztások megtekintése és kezelése](storsimple-8000-manage-alerts.md#configure-alert-settings) című részben leírt lépéseket.
  * A StorSimple virtuális tömb beállításainak megtekintéséhez és törléséhez kövesse a [StorSimple riasztások megtekintése és kezelése](storsimple-virtual-array-manage-alerts.md#configure-alert-settings) című részben leírt lépéseket.
- Azok a felhasználók, akik hozzáférhetnek a megosztásokon lévő adatokhoz. Megjelenik azoknak a felhasználóknak a listája, akik hozzáférhetnek a megosztási adatokhoz, és megtekinthetők. Ez a lista a megosztások törlésekor is törlődik. Ez csak a StorSimple virtuális tömbökre vonatkozik.
  * A megosztáshoz hozzáférő vagy törölhető felhasználók listájának megtekintéséhez kövesse a Megosztások kezelése a [StorSimple virtuális tömbben](storsimple-virtual-array-manage-shares.md) című rész lépéseit.

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Az alábbiakban néhány kérdést és választ a biztonság és a Microsoft Azure StorSimple.

**K:** A szolgálatom veszélybe került. Mi legyen a következő lépésem?

**A.** Azonnal módosítania kell a szolgáltatás adattitkosítási kulcsát és a tárfiók kulcsait a rétegzési adatokhoz használt tárfiókhoz. Az utasításokért látogasson el a következő

* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [A tárfiókok kulcsos elforgatása](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**K:** Nekem van egy új StorSimple eszköz, amely kéri a szolgáltatás regisztrációs kulcsot. Hogyan tudom letölteni?

**A.** Ez a kulcs a StorSimple Eszközkezelő szolgáltatás első létrehozásakor jött létre. Amikor a StorSimple Eszközkezelő szolgáltatással csatlakozik az eszközhöz, a szolgáltatás gyorskezdőlapjával megtekintheti vagy újragenerálhatja a szolgáltatás regisztrációs kulcsát. Új szolgáltatásregisztrációs kulcs létrehozása nem érinti a meglévő regisztrált eszközöket. Az utasításokért látogasson el a következő

* [A szolgáltatásregisztrációs kulcs megtekintése vagy újragenerálása](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**K:** Elvesztettem a szolgáltatásadat-titkosítási kulcsomat. Mit tegyek?

**A.** Lépjen kapcsolatba a Microsoft támogatási szolgálatával. Bejelentkezhetnek az eszköz támogatási munkamenetére, és segíthetnek a kulcs lekérésben (feltéve, hogy legalább egy eszköz online állapotban van). Közvetlenül a szolgáltatás adattitkosítási kulcsának beszerzése után módosítsa azt annak érdekében, hogy az új kulcs csak Ön számára ismert legyen. Az utasításokért látogasson el a következő

* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**K:**  Engedélyeztem egy eszközt a szolgáltatás adattitkosítási kulcsának módosítására, de nem kezdtem el a kulcsmódosítási folyamatot. Mit tegyek?

**A.** Ha az időbeli elállási időszak lejárt, újra engedélyeznie kell az eszközt a szolgáltatásadat-titkosítási kulcs módosításához, és újra kell indítania a folyamatot.

**K:**  Megváltoztattam a szolgáltatás adattitkosítási kulcsát, de 4 órán belül nem tudtam frissíteni a többi eszközt. Újra kell kezdenem?

**A.** A 4 órás időszak csak a változás megváltására szolgál. Miután elindítja a frissítési folyamatot a hivatalos StorSimple eszközön, az engedélyezés addig érvényes, amíg az összes eszköz nem frissül.

**K:** A StorSimple adminisztrátorunk elhagyta a céget. Mit tegyek?

**A.** Módosítsa és állítsa alaphelyzetbe a StorSimple-eszközhöz való hozzáférést lehetővé tenni lehetővé tévő jelszavakat, és módosítsa a szolgáltatás adattitkosítási kulcsát annak érdekében, hogy az új adatok illetéktelen személyek számára ne legyenek ismertek. Az utasításokért látogasson el a következő

* [A StorSimple Eszközkezelő szolgáltatás használata a storsimple jelszavak módosításához](storsimple-8000-change-passwords.md)
* [A szolgáltatásadat-titkosítási kulcs módosítása](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Chap konfigurálása a StorSimple eszközhöz](storsimple-8000-configure-chap.md)

**K:** Meg szeretném adni a StorSimple Snapshot Manager jelszavát egy olyan állomásnak, amely a StorSimple eszközhöz csatlakozik, de a jelszó nem érhető el. Mit tehetnék?

**A.** Ha elfelejtette a jelszót, hozzon létre egy újat. Ezután győződjön meg róla, hogy tájékoztassa az összes meglévő felhasználót, hogy a jelszó megváltozott, és hogy frissíteniük kell az ügyfelek et az új jelszó használatához. Az utasításokért látogasson el a következő

* [A StorSimple Snapshot Manager jelszavának módosítása](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Eszköz hitelesítése](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**K:** A StorSimple-hez való Windows PowerShell távoli eléréséhez való távoli hozzáférés tanúsítványa megváltozott az eszközön. Hogyan frissíthetem a távelérésű ügyfeleimet?

**A.** Az új tanúsítványt letöltheti a StorSimple Eszközkezelő szolgáltatásból, majd biztosíthatja, hogy telepítve legyen a távelérésű ügyfelek tanúsítványtárolójában. Az utasításokért látogasson el a következő

* [Import-tanúsítvány parancsmag](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**K:** Védve van az adataim, ha a StorSimple Eszközkezelő szolgáltatás sérült?

**A.** A szolgáltatás konfigurációs adatai mindig titkosítva vannak a nyilvános kulccsal, amikor webböngészőben tekinti meg őket. Mivel a szolgáltatás nem fér hozzá a személyes kulcshoz, a szolgáltatás nem fogja látni az adatokat. Ha a StorSimple Eszközkezelő szolgáltatás sérült, nincs hatása, mivel nincsenek a StorSimple Eszközkezelő szolgáltatásban tárolt kulcsok.

**K:** Ha valaki hozzáfér az adattitkosítási tanúsítványhoz, az adataim biztonsága sérül?

**A.** A Microsoft Azure titkosított formátumban tárolja az ügyfél adattitkosítási kulcsát (.pfx fájlt). Mivel a .pfx fájl titkosított, és a StorSimple szolgáltatás nem rendelkezik a .pfx fájl visszafejtéséhez szükséges szolgáltatásadat-titkosítási kulccsal, a .pfx fájlhoz való hozzáférés egyszerűen nem tár fel titkokat.

**K:** Mi történik, ha egy kormányzati szervezet kéri a Microsofttól az adataimat?

**A.** Mivel az összes adat titkosítva van a szolgáltatáson, és a személyes kulcs az eszközzel együtt marad, a kormányzati entitásnak meg kell kérnie az ügyfelet az adatokról.



## <a name="next-steps"></a>További lépések

[Telepítse a StorSimple eszközt.](storsimple-8000-deployment-walkthrough-u2.md)

