---
title: Azure Storage használatával PaaS alkalmazások védelme |} Microsoft Docs
description: " Tudnivalók Azure Storage biztonsági gyakorlati tanácsok a PaaS webes és mobilalkalmazásokhoz biztonságossá tételéhez. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/20/2018
ms.author: TomShinder
ms.openlocfilehash: ffc04973a003c65f52f3387292f11fede65edce3
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295295"
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>PaaS webes és mobilalkalmazások Azure Storage használatával biztonságossá tétele

Ez a cikk arról lesz szó Azure Storage ajánlott biztonsági eljárások az PaaS webes és mobilalkalmazások védelme gyűjteménye. Az alábbi gyakorlati tanácsok az Azure-ral tapasztalatunk és az ügyfelek, például a saját kezűleg feladatait származik.

A [Azure Storage biztonsági útmutató](../storage/common/storage-security-guide.md) részletes információt az Azure Storage és a biztonsági nagyszerű forrás.  Ez a cikk foglalkozik magas szinten azokat a fogalmakat, a biztonsági útmutató és a biztonsági útmutatója tartalmazza, valamint a más forrásokból, további információt mutató hivatkozások találhatók.

## <a name="azure-storage"></a>Azure Storage

Azure lehetővé teszi a üzembe helyezéséről és használatáról a tárolási módon könnyen elérhető helyi. Az Azure storage méretezhetőségi és viszonylag kevés munkát rendelkezésre állási magas szintű érheti el. Nem csak az Azure storage alapját a Windows és Linux Azure Virtual Machines, azt is képes támogatni nagy elosztott alkalmazások.

Az Azure Storage a következő négy szolgáltatást biztosítja: Blob Storage, Table Storage, Queue Storage és File storage. További tudnivalókért lásd: [Microsoft Azure Storage bemutatása](../storage/storage-introduction.md).

## <a name="best-practices"></a>Ajánlott eljárások

Ez a cikk foglalkozik az alábbi gyakorlati tanácsokat:

- Hozzáférés-védelem:
   - Közös hozzáférésű jogosultságkódok (SAS)
   - Szerepköralapú hozzáférés-vezérlés (RBAC)

- Tárolás titkosítása:
   - Ügyféloldali titkosítása az értékes adatok
   - Storage Service Encryption

## <a name="access-protection"></a>Hozzáférés-védelem

### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Közös hozzáférésű Jogosultságkód használata helyett a tárfiók kulcsa

Infrastruktúra-szolgáltatási megoldásban, általában a Windows Server vagy Linux rendszerű virtuális gépek futó fájlokat védi a közzététel és hozzáférés-vezérlő mechanizmusok használatával ügyfelén fenyegetések. Használja a Windows [hozzáférés-vezérlési listák (ACL)](../virtual-network/virtual-networks-acl.md) és valószínűleg használna Linux [chmod](https://en.wikipedia.org/wiki/Chmod). Alapvetően Ez az pontosan mit kellene tennie, ha a kiszolgálón a saját adatközpont napjainkban volt védelmét.

A PaaS nem egyezik. A leggyakrabban használt módszereket fájlokat tárolnak a Microsoft Azure egyik használandó [Azure Blob Storage tárolóban](../storage/storage-dotnet-how-to-use-blobs.md). A Blob storage és az egyéb a file storage eltérése a fájl i/o, de a fájl i/o járó védelmi módszert.

Hozzáférés-vezérlés fontos. Segítséget hozzáférés az Azure storage, a rendszer létrehoz két 512 bites tárfiókkulcsok (SAKs) amikor Ön [hozzon létre egy tárfiókot](../storage/common/storage-create-storage-account.md). A kulcs redundanciájának szintjét elkerülhetők a szolgáltatás megszakítás során rutin kulcs Elforgatás lehetővé teszi.

Tárelérési kulcsok magas prioritású titkok és csak akkor érhető el a tároló hozzáférés-vezérlés felelős. A megfelelő személyek férhetnek hozzá a ezeket a kulcsokat, ha azok rendelkezik teljes körű vezérlést biztosítanak a tárolási és sikerült cserélje le, törlése vagy lesz fájlok hozzáadni a tárhelyhez. Ez magában foglalja a kártevő szoftverek és más potenciálisan csökkenthetik munkahelye vagy az ügyfelek tartalmat.

Továbbra is szükségük van egy módszerre, hozzáférést biztosít a tároló objektumok. Részletesebb hozzáférést kihasználhatja az [közös hozzáférésű Jogosultságkód](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). A SAS lehetővé teszi, hogy egy előre meghatározott időtartam és a különleges engedélyekkel rendelkező megosztott a tároló az adott objektumaiban. Egy közös hozzáférésű Jogosultságkód adhatók meg:

- A keresztül, amely a SAS érvényességi időtartama, beleértve a kezdési idő és a lejárati időpont.
- A biztonsági Társítások engedélyekre. Például a blob SAS előfordulhat, hogy adjon egy felhasználó olvasási és írási engedéllyel, hogy a blob, de nem törli az engedélyeket.
- Nem kötelező IP-cím vagy az IP-címek tartománya, amelyből Azure Storage a SAS fogad el. Például megadhatja egy adott IP-címek a szervezethez tartozó. Ez lehetővé teszi a biztonsági Társítások biztonsági egy másik mérték.
- A protokoll, amelyben Azure Storage a SAS fogad el. Ez nem kötelező paraméter használatával korlátozza a hozzáférést az ügyfelek HTTPS-kapcsolaton keresztül.

SAS lehetővé teszi, hogy azt nem átruházása a Tárfiók kulcsait a kívánt tartalmak megosztása. Mindig az alkalmazásban használt SAS módja a biztonságos a tárolási erőforrások megosztása a tárfiók kulcsait veszélyeztetése nélkül.

További tudnivalókért lásd: [megosztott hozzáférési aláírásokkal használatával](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). Az esetleges kockázatokat és javaslatok a kockázatok csökkentése érdekében kapcsolatos további információkért lásd: [ajánlott eljárásokat, amikor a SAS használatával](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés használata

Korábban tárgyalt közös hozzáférésű Jogosultságkód (SAS) használatával a tárfiókban lévő objektumokhoz korlátozott hozzáférés biztosítása más ügyfelek számára anélkül, hogy a fiók tárfiók kulcsára. Egyes esetekben a tárfiók egy adott művelethez kapcsolódó kockázatokat járó SAS előnyeit. Egyes esetekben is egyszerűbb, egyéb módon kezelésére.

Egy másik módja való hozzáférés kezelése [átruházásához hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC). Az RBAC, van szükségük, a pontos engedélyeket ad az alkalmazottak a fókusz alapján a szükséges mértékű ismeretek és a legalacsonyabb jogosultsági biztonsági alapelveket. Túl sok engedélyeket is elérhetővé teheti a támadásokkal fiókkal. Túl kevés engedélyek, az azt jelenti, hogy az alkalmazottak nem munkavégzéséhez hatékony. Az RBAC segít a részletes hozzáféréskezelést az Azure felajánlásával oldja meg a problémát. Ez elengedhetetlen a szervezeteknek, amelyek az adatok biztonsági házirendek kikényszerítéséhez.

Kihasználhatja a beépített RBAC-szerepkörök felhasználók jogosultságok hozzárendelése az Azure-ban. Fontolja meg a felhő üzemeltetői az, hogy a storage-fiókok és kezelheti a klasszikus tárfiókokba klasszikus tárolási fiók közreműködői szerepkört kell tárolási fiók közreműködői használatát. A felhő üzemeltetői számára, amelyeknek szükségük van az kezelheti a virtuális gépek, de nem a virtuális hálózati vagy tárolási fiókot, amelyhez csatlakoznak, fontolja meg azokat a virtuális gép közreműködő szerepkört.

A szervezeteknek, amelyek kényszeríti ki a hozzáférés-vezérlés képességeinek például RBAC által előfordulhat, hogy kell jogosultságot ad mint azok a felhasználók számára szükséges további engedélyekkel. Ez azzal, hogy a felhasználók adatokat először nem rendelkeznek hozzáféréssel lehetővé adatok biztonsági sérülés vezethet.

További információt az RBAC lásd:

- [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md)
- [Az Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../role-based-access-control/built-in-roles.md)
- [Az Azure Storage biztonsági útmutató](../storage/common/storage-security-guide.md) biztonságossá tétele az RBAC a tárfiók a részletek

## <a name="storage-encryption"></a>Storage-titkosítás

### <a name="use-client-side-encryption-for-high-value-data"></a>Ügyféloldali titkosítás használata értékes adatok

Ügyféloldali titkosítás segítségével szoftveresen fejteni az adatokat, amikor fogadása a tárolási és programozott módon titkosítja az adatokat átvitel közben Azure Storage feltöltés előtt.  Ez az átvitel során adatok titkosítását biztosítja, de emellett biztosítja az inaktív adatok titkosítását.  Ügyféloldali titkosítás a legbiztonságosabb módszer titkosítja az adatokat, de azt kell programozott módosítja az alkalmazás és kulcskezelés folyamatok vezetnek be.

Ügyféloldali titkosítás is lehetővé teszi a titkosítási kulcsok kizárólagos hozzáféréssel rendelkeznek.  Ön hozza létre, és a saját titkosítási kulcsok kezeléséhez.  Ügyféloldali titkosítás használ egy boríték technika állít elő, ha az Azure storage ügyféloldali kódtár a tartalom titkosítási kulcsot (CEK), ezt követően (titkosított) (KEK-) kulcs titkosítási kulcs használatával. A KEK kulcsazonosítójával azonosíthatók és aszimmetrikus kulcspár, vagy egy szimmetrikus kulcsot és is kezelhetők helyileg vagy tárolt [Azure Key Vault](../key-vault/key-vault-whatis.md).

Ügyféloldali titkosítás a Java és a .NET-storage ügyfélkódtáraival van beépítve.  Lásd: [ügyféloldali titkosítás és a Microsoft Azure tárolás az Azure Key Vault](../storage/storage-client-side-encryption.md) lévő adatok ügyfélalkalmazásokon belüli titkosítását és létrehozása és kezelése a saját titkosítási kulccsal.

### <a name="storage-service-encryption"></a>Storage Service Encryption

Ha [Storage szolgáltatás titkosítási](../storage/storage-service-encryption.md) a File storage használata engedélyezett, az adatok titkosítása automatikusan az AES-256 titkosítás használatával. A Microsoft kezeli a titkosítási, visszafejtési és kulcskezelést. Ez a szolgáltatás LRS, GRS és redundancia típus esetében érhető el.

## <a name="next-steps"></a>További lépések

Ez a cikk bevezetett Azure Storage ajánlott biztonsági eljárások az PaaS webes és mobilalkalmazások védelme gyűjteménye. A PaaS üzemelő példányok biztosításával kapcsolatos további tudnivalókért lásd:

- [PaaS-környezetek védelme](security-paas-deployments.md)
- [A PaaS webes és mobilalkalmazásokhoz, az Azure App Services biztonságossá tétele](security-paas-applications-using-app-services.md)
- [Az Azure-ban PaaS-adatbázisok védelme](security-paas-applications-using-sql.md)
