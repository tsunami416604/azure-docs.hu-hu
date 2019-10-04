---
title: Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával
description: További információ a közös hozzáférésű aláírások (SAS) használatáról az Azure Storage-erőforrásokhoz való hozzáférés delegálásához, beleértve a blobokat, a várólistákat, a táblákat és a fájlokat.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 0410da26a2ea5811c5a107ce233f2442b60fd9ca
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2019
ms.locfileid: "71670831"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával

A közös hozzáférésű aláírás (SAS) biztonságos delegált hozzáférést biztosít a Storage-fiók erőforrásaihoz anélkül, hogy veszélyeztetné az adatai biztonságát. A SAS segítségével részletesen szabályozhatja, hogy az ügyfél Hogyan férhet hozzá az adataihoz. Megadhatja, hogy az ügyfél milyen erőforrásokhoz férhet hozzá, milyen engedélyekkel rendelkezik ezekhez az erőforrásokhoz, és hogy mennyi ideig érvényes az SAS érvényessége, más paraméterek között.

## <a name="types-of-shared-access-signatures"></a>A közös hozzáférésű aláírások típusai

Az Azure Storage három különböző típusú közös hozzáférési aláírást támogat:

- **Felhasználói delegálási SAS (előzetes verzió).** A felhasználói delegálási SAS Azure Active Directory (Azure AD) hitelesítő adatokkal, valamint a SAS számára megadott engedélyekkel van védve. A felhasználói delegálási SAS csak a blob Storage-ra vonatkozik. Felhasználói delegálási SAS létrehozásához először egy felhasználói delegálási kulcsot kell igényelnie, amely az SAS aláírására szolgál. A felhasználói delegálási SAS-vel kapcsolatos további információkért lásd: [felhasználói delegálási sas létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas).
- **Szolgáltatás SAS.** A Storage-fiók kulcsa a Service SAS védelmét biztosítja. A szolgáltatás SAS delegál egy erőforráshoz való hozzáférést az Azure Storage-szolgáltatások egyikében: BLOB Storage, üzenetsor-tároló, Table Storage vagy Azure Files. További információ a szolgáltatás SAS-ről: [Service sas létrehozása (REST API)](/rest/api/storageservices/create-service-sas).
- **Fiók SAS.** A fiók SAS védelmét a Storage-fiók kulcsa biztosítja. A fiókalapú SAS egy vagy több tárolószolgáltatás erőforrásaihoz nyújt hozzáférést. A szolgáltatáson vagy a felhasználói delegálási SAS-n keresztül elérhető összes művelet a fiók SAS-n keresztül is elérhető. Emellett a fiók SAS használatával delegálhatja a hozzáférést a szolgáltatás szintjén érvényes műveletekhez, például **lekérheti/beállíthatja a szolgáltatás tulajdonságait** , és **lekérheti a szolgáltatás-statisztika** műveleteit. A blobtárolók, táblák, üzenetsorok és fájlmegosztások olvasási, írási és törlési műveleteihez is hozzáférést biztosíthat, amelyeket a szolgáltatásalapú SAS nem engedélyez. A fiók SAS-vel kapcsolatos további információkért [hozzon létre egy FIÓKOT sas (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait akkor használja, ha a fiók kulcsa helyett biztonsági szempontból ajánlott, ami könnyebben sérülhet. Ha az alkalmazás kialakításához közös hozzáférésű aláírásokra van szükség a blob Storage-hoz való hozzáféréshez, az Azure AD-beli hitelesítő adatok használatával hozzon létre egy felhasználói delegálási SAS-t, ha lehetséges,

A közös hozzáférésű aláírás két űrlap egyikét veheti igénybe:

- **Ad hoc SAS:** Ha ad hoc SAS-t hoz létre, a kezdési idő, a lejárati idő és az SAS-engedélyek mind meg vannak adva az SAS URI-ban (vagy hallgatólagosan, ha a kezdési idő nincs megadva). Bármilyen típusú SAS lehet ad hoc SAS.
- **A tárolt hozzáférési házirenddel rendelkező Service SAS:** Egy tárolt hozzáférési szabályzat definiálva van egy erőforrás-tárolón, amely lehet blob-tároló,-tábla,-várólista vagy-fájlmegosztás. A tárolt hozzáférési szabályzat segítségével kezelheti a korlátozásokat egy vagy több szolgáltatás közös hozzáférési aláírásakor. Amikor egy szolgáltatás SAS-t társít egy tárolt hozzáférési szabályzathoz, az SAS örökli a @ no__t-0the kezdési idejét, lejárati idejét és az engedélyek @ no__t-1defined a tárolt hozzáférési házirendhez.

> [!NOTE]
> A felhasználói delegálási SAS vagy a fiók SAS-nak ad hoc SAS-nek kell lennie. A tárolt hozzáférési szabályzatok nem támogatottak a felhasználói delegálási SAS vagy a fiók SAS számára.

## <a name="how-a-shared-access-signature-works"></a>Hogyan működik a közös hozzáférés aláírása?

A közös hozzáférésű aláírás egy aláírt URI, amely egy vagy több tárolási erőforrásra mutat, és olyan tokent tartalmaz, amely a lekérdezési paraméterek speciális készletét tartalmazza. A jogkivonat azt jelzi, hogy az ügyfél milyen módon férhet hozzá az erőforrásokhoz. A lekérdezési paraméterek egyike, az aláírás a SAS-paraméterekből lett kialakítva, és az SAS létrehozásához használt kulccsal van aláírva. Az Azure Storage ezt az aláírást használja a tárolási erőforráshoz való hozzáférés engedélyezéséhez.

### <a name="sas-signature"></a>SAS-aláírás

Az SAS-t kétféleképpen lehet aláírni:

- Azure Active Directory (Azure AD) hitelesítő adatok használatával létrehozott felhasználói delegálási kulccsal. Felhasználói delegálási SAS van aláírva a felhasználói delegálási kulccsal.

    A felhasználói delegálási kulcs beszerzéséhez és az SAS létrehozásához egy Azure AD rendszerbiztonsági tag hozzá kell rendelni egy szerepköralapú hozzáférés-vezérlési (RBAC) szerepkört, amely tartalmazza a **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. A felhasználói delegálási kulcs beszerzéséhez szükséges engedélyekkel rendelkező RBAC-szerepkörökkel kapcsolatos részletes információkért lásd: [felhasználói delegálási sas létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas).

- A Storage-fiók kulcsaként. A Service SAS és a fiók SAS is a Storage-fiók kulcsával van aláírva. A fiók kulccsal aláírt SAS létrehozásához az alkalmazásnak hozzá kell férnie a fiók kulcsához.

### <a name="sas-token"></a>SAS-jogkivonat

Az SAS-jogkivonat egy olyan karakterlánc, amelyet az ügyfél oldalán állít elő, például az egyik Azure Storage ügyféloldali kódtára használatával. A SAS-tokent semmilyen módon nem követik nyomon az Azure Storage. Korlátlan számú SAS-tokent hozhat létre az ügyféloldali oldalon. Miután létrehozott egy SAS-t, terjesztheti azt olyan ügyfélalkalmazások számára, amelyek hozzáférést igényelnek a Storage-fiók erőforrásaihoz.

Ha egy ügyfélalkalmazás egy kérelem részeként egy SAS URI-t biztosít az Azure Storage-hoz, a szolgáltatás ellenőrzi a SAS paramétereit és aláírását annak ellenőrzéséhez, hogy érvényes-e a kérelem engedélyezésére. Ha a szolgáltatás ellenőrzi, hogy érvényes-e az aláírás, akkor a kérelem engedélyezve lesz. Ellenkező esetben a kérelem elutasítása a 403-as hibakódtal (tiltott) történik.

Íme egy példa a Service SAS URI-ra, amely az erőforrás URI azonosítóját és az SAS-tokent mutatja:

![A Service SAS URI összetevői](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Mikor kell megosztott hozzáférési aláírást használni

Használjon SAS-t, ha biztonságos hozzáférést szeretne biztosítani a Storage-fiók erőforrásaihoz bármely olyan ügyfél számára, aki egyébként nem rendelkezik engedéllyel az adott erőforrásokhoz.

Gyakori eset, ha egy SAS hasznos szolgáltatás, ahol a felhasználók a saját adataikat olvassák és írhatják a Storage-fiókjába. Olyan helyzetekben, amikor egy Storage-fiók felhasználói adattárolást tárol, két jellemző kialakítási minta létezik:

1. Az ügyfelek az előtér-proxy szolgáltatáson keresztül tölthetik le és tölthetik le az adatletöltést, amely hitelesítést végez. Az előtér-proxy szolgáltatásnak megvan az előnye, hogy lehetővé teszi az üzleti szabályok érvényesítését, de nagy mennyiségű vagy nagy mennyiségű adatforgalom esetén az igényeknek megfelelően méretezhető szolgáltatás létrehozása költséges vagy nehézkes lehet.

   ![Forgatókönyv diagramja: Előtér-proxy szolgáltatás](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Egy egyszerű szolgáltatás szükség szerint hitelesíti az ügyfelet, majd létrehoz egy SAS-t. Miután az ügyfélalkalmazás megkapja az SAS-t, közvetlenül hozzáférhetnek a Storage-fiók erőforrásaihoz a SAS által meghatározott engedélyekkel és az SAS által engedélyezett időtartammal. Az SAS csökkenti az összes, az előtér-proxy szolgáltatáson keresztüli útválasztási művelet szükségességét.

   ![Forgatókönyv diagramja: SAS-szolgáltató szolgáltatás](./media/storage-sas-overview/sas-storage-provider-service.png)

Számos valós szolgáltatás a két módszer hibrid használatát is felhasználhatja. Előfordulhat például, hogy egyes adatforrásokat az előtér-proxyn keresztül lehet feldolgozni és érvényesíteni, míg más adatmentést és/vagy közvetlenül az SAS használatával.

Emellett az SAS-nek egy másolási műveletben a Forrásobjektum elérésének engedélyezéséhez bizonyos helyzetekben a következő esetekben van szükség:

- Ha egy másik Storage-fiókban található blobba másol egy blobot, a forrás blob elérésének engedélyezéséhez SAS-t kell használnia. A cél blobhoz való hozzáférés engedélyezéséhez használhat SAS-t is.
- Ha egy másik Storage-fiókban található fájlra másol egy fájlt, a forrásfájl elérésének engedélyezéséhez SAS-t kell használnia. Igény szerint az SAS használatával is engedélyezheti a célfájl elérését.
- Amikor fájlokat másol egy fájlba, vagy egy fájlt egy blobba, egy SAS-t kell használnia a Forrásobjektum hozzáférésének engedélyezéséhez, még akkor is, ha a forrás-és a célhely ugyanazon a Storage-fiókon belül található.

## <a name="best-practices-when-using-sas"></a>Ajánlott eljárások SAS használatakor

Ha közös hozzáférési aláírásokat használ az alkalmazásokban, a következő két lehetséges kockázattal kell tisztában lennie:

- Ha egy SAS kiszivárgott, azt bárki felhasználhatja, aki beolvassa azt, ami potenciálisan veszélyeztetheti a Storage-fiókját.
- Ha egy ügyfélalkalmazás számára megadott SAS lejár, és az alkalmazás nem tud beolvasni egy új SAS-t a szolgáltatásból, akkor az alkalmazás működésének akadálya lehet.

A közös hozzáférési aláírások használatára vonatkozó alábbi javaslatok segíthetnek a kockázatok enyhítésében:

- A SAS-t **mindig a https használatával** hozzon létre vagy terjesszen. Ha a SAS átadása HTTP-n keresztül történik, és a rendszer elvégzi a támadók számára a támadók által végrehajtott támadásokat, megtekintheti az SAS-t, majd ugyanúgy használhatja azt, mint amennyire a kívánt felhasználó rendelkezhet, esetleg veszélyezteti a bizalmas adatokat, vagy engedélyezheti az adatsérülést a rosszindulatú felhasználótól.
- **Ha lehetséges, használjon felhasználói delegálási SAS-t.** A felhasználói delegálási SAS magas szintű biztonságot nyújt a szolgáltatás SAS vagy egy fiók SAS számára. A felhasználói delegálási SAS védett az Azure AD hitelesítő adataival, így nem kell a fiókja kulcsát a kóddal tárolnia.
- **Egy SAS-re vonatkozó visszavonási terv van érvényben.** Győződjön meg arról, hogy készen áll a válaszadásra, ha a SAS biztonsága sérül.
- **Definiáljon egy tárolt hozzáférési szabályzatot egy szolgáltatás SAS számára.** A tárolt hozzáférési szabályzatok lehetővé teszik a Service SAS engedélyeinek visszavonását anélkül, hogy újra kellene generálni a Storage-fiók kulcsait. Állítsa a lejáratot a jövőben (vagy a végtelenre), és győződjön meg róla, hogy rendszeresen frissült, hogy a későbbiekben áthelyezze a szolgáltatást.
- **A közeljövőben lejárati idő használata egy ad hoc SAS-szolgáltatás SAS vagy fiók SAS esetében.** Ily módon, még akkor is, ha egy SAS biztonsága sérül, csak rövid ideig érvényes. Ez a gyakorlat különösen akkor fontos, ha nem hivatkozhat tárolt hozzáférési szabályzatra. A közeljövőben lejárati idő a blobba írt adatok mennyiségét is korlátozza a feltöltéshez rendelkezésre álló idő korlátozásával.
- **Ha szükséges, az ügyfelek automatikusan megújítják az SAS-t.** Az ügyfeleknek a lejárat előtt is meg kell újítaniuk a SAS-t, hogy újra lehessen próbálkozni az újrapróbálkozások idejével, ha az SAS-t biztosító szolgáltatás nem érhető el. Ha a SAS-t kis számú azonnali, rövid életű művelethez kívánja használni, amelyeket a lejárati időszakon belül el kell végezni, akkor ez szükségtelen lehet, mivel a SAS-t nem kell megújítani. Ha azonban olyan ügyfele van, amely az SAS-n keresztül rutinul kéri a kérelmeket, akkor a lejárati lehetőség a lejátszásra kerül. A legfontosabb szempont, hogy az SAS-nek rövid életűnek kell lennie (ahogy azt korábban már említettük) annak biztosításához, hogy az ügyfél kellő időben megújítsa a megújítást (a sikeres megújítás előtt lejáró SAS okozta fennakadások elkerülése érdekében).
- **Ügyeljen rá, hogy az SAS indítási ideje legyen.** Ha **most**beállítja a kezdő időpontot a sas számára, akkor az óra torzítása miatt (a különböző gépektől függően a jelenlegi idő eltérései) a hibák időnként megfigyelhetők az első néhány percben. Általában úgy állítsa be a kezdési időpontot, hogy legalább 15 perccel korábbi legyen. Vagy ne állítsa be egyáltalán, hogy minden esetben azonnal érvényes lesz. Ugyanez általában érvényes a lejárati időre is – ne feledje, hogy a kérések bármelyik irányában akár 15 percet is megfigyelheti. Az 2012-02-12-nál korábbi REST-verziót használó ügyfelek esetében a tárolt hozzáférési szabályzatra nem hivatkozó SAS maximális időtartama 1 óra, és a szabályzatok által meghiúsult hosszabb időszakot megadó házirendek.
- **Legyen egyedi az elérni kívánt erőforrással.** Az ajánlott biztonsági eljárás a minimálisan szükséges jogosultságokkal rendelkező felhasználó biztosítása. Ha egy felhasználónak csak olvasási hozzáférésre van szüksége egyetlen entitáshoz, akkor olvasási hozzáféréssel kell rendelkeznie az adott entitáshoz, és nem kell olvasási/írási/törlési hozzáférést adni az összes entitáshoz. Ez segít csökkenteni a károkat, ha a SAS biztonsága sérül, mert az SAS a támadók kezében kevesebb árammal rendelkezik.
- **Ismerje meg, hogy a fiók számlázása minden használat után történik, beleértve a SAS-n keresztül is.** Ha írási hozzáférést biztosít egy blobhoz, a felhasználó a 200 GB-os blob feltöltését is választhatja. Ha olvasási hozzáféréssel is rendelkezik, dönthetnek úgy is, hogy 10 alkalommal töltik le, ami 2 TB-ot jelent a kimenő forgalomért. A korlátozott engedélyek megadásával csökkentheti a rosszindulatú felhasználók lehetséges műveleteit. Használja a rövid élettartamú SAS-t a fenyegetés csökkentése érdekében (de legyen szem előtt tartva a Befejezés időpontját).
- **A SAS használatával írt adatellenőrzés.** Ha egy ügyfélalkalmazás adatot ír a Storage-fiókjába, vegye figyelembe, hogy problémák merülhetnek fel az adott adattal kapcsolatban. Ha az alkalmazás működéséhez az szükséges, hogy az adatok a használatra kész állapotban legyenek, akkor ezt az ellenőrzést az adatok írása után kell végrehajtani, mielőtt az alkalmazás használni fogja őket. Ez a gyakorlat védelmet biztosít a fiókba írt sérült vagy rosszindulatú adatbevitelek ellen, akár egy olyan felhasználó, aki megfelelően megszerezte az SAS-t, vagy egy kiszivárgott SAS-t használó felhasználó.
- **Ismerje meg, ha nem használ SAS-t.** Előfordulhat, hogy egy adott művelethez kapcsolódó kockázatok a Storage-fiókkal szemben meghaladják a SAS használatának előnyeit. Ilyen műveletekhez hozzon létre egy olyan középszintű szolgáltatást, amely az üzleti szabályok érvényesítése, hitelesítés és naplózás végrehajtása után a Storage-fiókjába ír. Időnként más módokon is egyszerűbb a hozzáférés kezelése. Ha például a tárolóban lévő összes blobot nyilvánosan olvashatóként szeretné végezni, akkor a tárolót nyilvánosra helyezheti ahelyett, hogy minden ügyfél számára elérhetővé tegye a hozzáférést.
- **Az alkalmazás figyeléséhez használja a Azure Monitor és az Azure Storage-naplókat.** A Azure Monitor és a Storage Analytics naplózásával megfigyelheti az engedélyezési hibákhoz tartozó összes tüskét az SAS-szolgáltató szolgáltatásban vagy a tárolt hozzáférési szabályzat véletlen eltávolítása miatt. További információ: [Azure Storage-metrikák Azure monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) és [Azure Storage Analytics naplózás](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="get-started-with-sas"></a>Az SAS első lépései

A közös hozzáférésű aláírások megkezdéséhez tekintse meg az egyes SAS-típusok következő cikkeit.

### <a name="user-delegation-sas"></a>Felhasználói delegálás SAS

- [Felhasználói delegálási SAS létrehozása egy tárolóhoz vagy blobhoz a PowerShell (előzetes verzió) használatával](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI-vel (előzetes verzió)](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz .NET-tel (előzetes verzió)](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Szolgáltatás SAS

- [Service SAS létrehozása tárolóhoz vagy blobhoz .NET-tel](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Fiók SAS

- [Fiók SAS létrehozása a .NET-tel](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>További lépések

- [Hozzáférés delegálása közös hozzáférési aláírással (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Felhasználói delegálási SAS létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Szolgáltatás SAS létrehozása (REST API)](/rest/api/storageservices/create-service-sas)
- [Fiók SAS létrehozása (REST API)](/rest/api/storageservices/create-account-sas)
