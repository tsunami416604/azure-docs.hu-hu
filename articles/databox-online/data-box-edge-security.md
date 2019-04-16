---
title: Box Edge által nyújtott Adatbiztonság |} A Microsoft Docs
description: A biztonsági és adatvédelmi szolgáltatásait ismerteti, amelyek a Data Box peremhálózati eszköz, a szolgáltatás és a helyszíni és felhőbeli adatok védelmére.
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 197c2c021dd9f674d196fb1169155bc6015f2e79
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578610"
---
# <a name="data-box-edge-security-and-data-protection"></a>Data Box Edge biztonság és adatvédelem

Biztonsági esetén a fő szempont bevezetése új technológia, különösen akkor, ha a technológiát a bizalmas vagy szellemi tulajdont képező adatokat használatos. A Microsoft Azure Data Box peremhálózati megoldás segítségével, győződjön meg arról, hogy csak hitelesített entitások is megtekintése, módosítása vagy törlése az adatok.

Ez a cikk ismerteti a Data Box Edge biztonsági funkciók, amelyek mindegyike a megoldás-összetevőket és az azokon tárolt adatok védelme érdekében.

Az Azure Data Box peremhálózati megoldás egymással kommunikáló négy fő összetevőből áll:

- **Data Box Edge / Data Box-átjáró szolgáltatás az Azure-ban üzemeltetett** – a felügyeleti erőforrás, amellyel az eszköz rendelés létrehozása, az eszköz konfigurálása és a rendelés nyomon követésével befejezését.
- **Data Box peremhálózati eszköz** – az átvitel eszköz, amely tartalmazza a szükséges, hogy a helyszíni adatok importálása az Azure-ba való.
- **Az eszközhöz csatlakoztatott ügyfelek /-gazdagépekre** – az ügyfelek az infrastruktúra, amely a Data Box peremhálózati eszköz csatlakozik, és adatokat tartalmaznak, amelyek kell védeni.
- **Felhőalapú tároló** – a hely az Azure-felhőben, ahol az adatok tárolása történik. Ez a hely általában a létrehozott Data Box Edge-erőforráshoz társított storage-fiók.


## <a name="data-box-edgedata-box-gateway-service-protection"></a>Box Edge/Data Box átjáró szolgáltatás adatvédelem

A Data Box Edge/Data Box Gateway szolgáltatás nem a Microsoft Azure-ban üzemeltetett felügyeleti szolgáltatás. A szolgáltatás konfigurálása és kezelése az eszköz segítségével.

- A Data Box Edge/Data Box Gateway szolgáltatás használatához a szervezet rendelkezik a nagyvállalati szerződés (EA) vagy a Cloud Solution Provider (CSP) előfizetésre. További információért ugorjon [Azure-előfizetéssel regisztrálhat](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)!
- A felügyeleti szolgáltatás az Azure-ban üzemel, mert védi az Azure biztonsági szolgáltatásait. A Microsoft Azure által biztosított biztonsági funkciókról a [Microsoft Azure biztonsági és adatkezelési központban](https://azure.microsoft.com/support/trust-center/security/) talál további információt.
- A felügyeleti műveletek SDK titkosítási kulcs érhető el a Data Box-Edge / Data Box-Gateway-erőforrásra **eszköztulajdonságok**. Megtekintheti a titkosítási kulcs csak akkor, ha rendelkezik engedéllyel az erőforrás Graph API-hoz.

## <a name="data-box-edge-device-protection"></a>Adatvédelem Box peremhálózati eszköz

A Data Box peremhálózati eszköz egy helyszíni eszköz, amely segít az adatok átalakítása a helyi feldolgozásra, és elküldi azt az Azure-ban. Az eszköz:

- Szüksége van egy aktiválási kulcsot a Data Box Edge/Data Box Gateway szolgáltatás eléréséhez.
- Van minden alkalommal eszköz jelszóval védett.
- A zárolt eszköz van. Az eszköz BMC- és BIOS-ban, a BIOS-ban korlátozott felhasználói hozzáféréssel rendelkező jelszóval védett.
- A biztonságos rendszerindítás engedélyezve van.
- A Windows Defender Device Guard futtatja. A Device Guard lehetővé teszi, hogy csak az a kódintegritási házirendekben meghatározott megbízható alkalmazások futtathatók.
- A borító zárolja az eszközt használható belül kulccsal rendelkezik. Azt javasoljuk, hogy az eszköz konfigurálása után nyissa meg a lefedik. Keresse meg a kulcsot, és a cover található az eszközön található adatlemezek bármely jogosulatlan hozzáférés megelőzése érdekében zárolja.

### <a name="protect-the-device-via-activation-key"></a>Az eszköz aktiválási kulccsal védelme

Csatlakozás a Data Box Edge/Data Box Gateway szolgáltatás, amely az Azure-előfizetésében létrehozott csak jogosult Data Box peremhálózati eszköz engedélyezett. Egy eszköz hitelesítéséhez, az aktiválási kulcs aktiválásához az eszköz a Data Box Edge/Data Box Gateway szolgáltatással kell használnia. További információért ugorjon [aktiválási kulcs beszerzése](data-box-edge-deploy-prep.md#get-the-activation-key).

Az aktiválási kulcs, amelyet használhat:

- Az Azure Active Directory (AAD) alapú hitelesítési kulcs.
- Három nap után lejár.
- Nem használt eszköz aktiválása után lépnek érvénybe.
 
Eszköz aktiválása után jogkivonatok használatával kommunikálni a Microsoft Azure.

### <a name="protect-the-device-via-password"></a>Jelszó-n keresztül az eszköz védelme

Jelszavak győződjön meg arról, hogy az adatok csak a jogosult felhasználók számára is elérhető. Data Box Edge és a Data Box-Gateway eszközök rendszerindítás zárolt állapotban.

A következőket teheti:

- A helyi webes felhasználói felületen, az eszköz egy böngészőből csatlakozzon, és adja meg egy jelszót az eszköz bejelentkezik.
- Távoli csatlakozás az eszköz PowerShell-felületén az HTTP-n keresztül. Távfelügyelet alapértelmezés szerint be van kapcsolva. Majd adja meg, jelentkezzen be az eszköz az eszköz jelszavát. További információért ugorjon [távolról csatlakozhat a Data Box peremhálózati eszköz](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface).

Tartsa szem előtt az alábbi gyakorlati tanácsokat:

- A Data Box Edge szolgáltatást nem sikerült beolvasni a már meglévő jelszavai: Ez csak alaphelyzetbe állíthatja őket az Azure Portalon keresztül. Azt javasoljuk, hogy minden jelszót biztonságos helyen tárolja el, így jelszó alaphelyzetbe állítása, ha elfelejti, nem kell. Ha a jelszó alaphelyzetbe állítása, mindenképpen azt alaphelyzetbe állítása előtt az összes felhasználó értesítése.
- Használja a helyi webes felhasználói Felületét, hogy [módosítsa a jelszót](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access). Ha megváltoztatja a jelszót, mindenképpen az összes távelérési a felhasználók értesítése, hogy azok nem tapasztalnak egy bejelentkezési hiba.
- A Windows PowerShell felületet az eszköz távoli kapcsolaton keresztül érheti el a HTTP. Bevált biztonsági gyakorlat csak a megbízható hálózathoz kell a HTTP Protokollt használja.
- Győződjön meg arról, hogy eszköz jelszavak erős és a magas szintű védelmet. Kövesse a [ajánlott eljárások a jelszavakhoz](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management).

## <a name="protect-the-data"></a>Az adatok védelme

Ez a szakasz ismerteti a Data Box Edge biztonsági funkciók, amelyek az átvitt adatokat és a tárolt adatok védelméhez.

### <a name="protect-data-at-rest"></a>Inaktív adatok védelme

Az adatok inaktív: a

- Az adatok inaktív a Data Box Edge BitLocker XTS-AES-256 titkosítással használja a helyi adatok védelme érdekében.
- Az adatok a megosztásokat a hozzáférés korlátozva.

    - A megosztás adatokhoz hozzáférő SMB-ügyfelek esetében van szükségük a megosztás társított felhasználói hitelesítő adatokat. Ezeket a hitelesítő adatokat határozza meg a megosztás létrehozása idején.
    - Az NFS-ügyfelek, amelyek éri el a megosztásokat az ügyfelek IP-címét kell adható hozzá a megosztás létrehozásakor.


### <a name="protect-data-in-flight"></a>Továbbított adatok védelme

A data-az-repülési:

- Olyan adatok, amely az eszköz és az Azure között, a szabványos TLS 1.2 használnak. A TLS 1.1-es és korábbi nincs tartalék van. Ha a TLS 1.2-es nem támogatott. az ügynök kommunikációja blokkolva lesznek. A TLS 1.2-es is portál és az SDK felügyeleti műveletekhez szükség.
- Amikor az ügyfelek hozzáférnek az eszköz a böngészőben helyi webes felületén keresztül, a szabványos TLS 1.2-es biztonságos alapértelmezett protokollként szolgál.

    - Az ajánlott eljárás, hogy a böngészőben a TLS 1.2 használatára.
    - Ha a böngésző nem támogatja a TLS 1.2, TLS 1.1-es vagy a TLS 1.0 is használhatja.
- Az adatok védelme érdekében, ha a data-kiszolgálókról másolja, azt javasoljuk, hogy használja-e SMB 3.0-s titkosítással.

### <a name="protect-data-via-storage-accounts"></a>Storage-fiókok keresztül adatok védelme

Az eszköz kapcsolódik egy storage-fiókot, amely az adatok az Azure-ban egy célhelyként szolgál. A tárfiókhoz való hozzáférést vezérlik az előfizetést és a két 512 bites tárelérési eléréséhez a storage-fiókhoz társított kulcsokat.

A kulcsok egyikét a hitelesítéshez során használatos a Data Box peremhálózati eszköz hozzáfér a tárfiókhoz. A másik hívóbetűt tárolt tartalék, amely lehetővé teszi a kulcsok rotálására rendszeres időközönként.

Biztonsági okokból a sok adatközpontok kulcsrotálás szükséges. Javasoljuk, hogy kövesse az ajánlott eljárások kulcsrotálás:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Alaposan a fiókja kulcsának védelmére. Nem terjesztheti más felhasználók jelszavát, keményen code, vagy mentse bárhol, mások számára elérhető egyszerű szöveges fájlban.
- [A fiók kulcsának újragenerálásával](../storage/common/storage-account-manage.md#regenerate-access-keys) az Azure portal használatával, ha úgy véli, előfordulhat, hogy sérült a biztonsága.
- Forgatás, majd [a tárfiókkulcsok szinkronizálása](data-box-gateway-manage-shares.md#sync-storage-keys) rendszeresen annak érdekében, hogy a tárfiók nem érhető el a jogosulatlan felhasználók.
- Rendszeres időközönként az Azure-rendszergazda kell módosítani, vagy az elsődleges vagy másodlagos kulcsának újragenerálása: közvetlenül hozzáférni a tárfiókhoz a Storage szakaszban az Azure Portal használatával.


## <a name="manage-personal-information"></a>Személyes adatok kezelése

A Data Box-Edge / Data Box-átjáró szolgáltatás adatokat gyűjt személyes adatokat a következő kulcs példányok:

- **Megrendelés részletei** – miután létrejött a megrendelés, a felhasználók szállítási címét, e-mail-címét és kapcsolattartási adatait az Azure Portal tárolja. A mentett információk a következők:
  - Kapcsolattartó neve
  - Telefonszám
  - E-mail
  - Utca, házszám
  - Város
  - Irányítószám
  - Állapot
  - Ország/tartomány/régió
  - Szállítmány nyomkövetési száma

    A rendelés részleteit vannak titkosítva, és a szolgáltatásban tárolt. A szolgáltatás mindaddig megőrzi az adatokat, amíg nem törli az erőforrást vagy rendelés explicit módon. Ezenkívül az erőforrás- és a megfelelő sorrendben törlését az idő az eszköz tartalmazza a szükséges mindaddig, amíg az eszköz visszatér a Microsoft blokkolva van.

- **Szállítási cím** – után a megrendeléskor Data Box szolgáltatás a szállítási cím biztosítja a külső szállítók, például a UPS.

- **Felhasználók megosztása** – az eszköz felhasználója is elérheti az adatokat, a megosztások elhelyezkedhet. Felhasználók férhetnek hozzá a megosztás adataihoz listája jelenik meg, és tekinthetnek meg. Ezt a listát a megosztás törlése esetén is törlődik. A felhasználók férhetnek hozzá, vagy törli a megosztást listájának megtekintéséhez kövesse [a Data Box Edge-megosztások kezelése](data-box-gateway-manage-shares.md).

További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>További lépések

[A Data Box Edge-eszköz üzembe helyezése](data-box-edge-deploy-prep.md).

