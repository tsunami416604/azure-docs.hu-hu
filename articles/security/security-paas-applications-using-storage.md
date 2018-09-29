---
title: Az Azure Storage használata PaaS-alkalmazások védelme |} A Microsoft Docs
description: Ismerje meg az Azure Storage biztonsági ajánlott eljárások a PaaS webes és mobilalkalmazások védelme.
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
ms.date: 09/28/2018
ms.author: TomShinder
ms.openlocfilehash: ac01aaca8c147b1f474b59ac57424f5cdc5f8a8d
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451867"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>PaaS web- és mobilalkalmazások az Azure Storage használatával biztonságossá tételének ajánlott eljárásait
Ebben a cikkben bemutatjuk az Azure Storage biztonsági védelmének bevált gyakorlata a platform--szolgáltatásként (PaaS) webes és mobilalkalmazások védelme gyűjteménye. Ajánlott eljárások az funkciót az Azure-ral és a az ügyfelek, például a saját maga származik.

Az Azure lehetővé teszi a tároló üzembe helyezése és módon könnyen elérhető helyi. Az Azure storage magas szintű méretezhetőség és rendelkezésre állása és viszonylag kis munkamennyiség érheti el. Nem csak az Azure Storage az alapítvány Windows és Linux Azure Virtual Machines esetén is alkalmas nagy méretű elosztott alkalmazások.

Az Azure Storage a következő négy szolgáltatást biztosítja: Blob storage, Table storage, Queue storage és File storage. További tudnivalókért lásd: [a Microsoft Azure Storage bemutatása](../storage/storage-introduction.md).

A [Azure Storage biztonsági útmutatóját](../storage/common/storage-security-guide.md) remek módja az Azure Storage és a biztonság részletes információkat. Ez – gyakorlati tanácsok cikk címek magas szinten azokat a fogalmakat találhatók a biztonsági útmutató és a biztonsági útmutató, valamint a más forrásokból, további információt mutató hivatkozásokat.

Ez a cikk foglalkozik az alábbi gyakorlati tanácsokat:

- Közös hozzáférésű jogosultságkódok (SAS)
- Szerepköralapú hozzáférés-vezérlés (RBAC)
- Értékes adatok ügyféloldali titkosítása
- Storage Service Encryption


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Tárfiókkulcs helyett közös hozzáférésű jogosultságkódok használata
Hozzáférés-vezérlés, kritikus fontosságú. Segítséget hozzáférés vezérlése az Azure Storage, Azure két 512 bites tárelérési fiók-kulcsokat hoz létre (SAKs) storage-fiók létrehozásakor. Kulcs redundancia szintjét is lehetővé teszi, hogy rutinszerű kulcsrotálás során szolgáltatáskiesések elkerülése. 

Tárelérési kulcsok magas prioritású titkos kódok, és csak akkor érhető el a storage-hozzáférés-vezérlés felelős. Ha illetéktelenek kap hozzáférést a kulcsokhoz, azok lesz teljes körű tárolási és sikerült cserélje le, törlése vagy fájlok hozzáadása a tárolóhoz. Ez magában foglalja a kártevők és más típusú tartalom, amely potenciálisan kedvezőtlenül befolyásolhatja a szervezet vagy az ügyfelek számára.

Továbbra is szükségük van egy módszerre a tárolási objektum hozzáférést biztosítanak. A részletes hozzáférést biztosítania a közös hozzáférésű jogosultságkód (SAS) előnyeit is igénybe vehet. A SAS megoszthatja a storage-ban meghatározott objektumokat egy előre meghatározott időtartam alatt, és meghatározott engedélyekkel lehetővé teszi. Közös hozzáférésű jogosultságkód definiálását teszi lehetővé:

- Az időköz keresztül, amely a SAS az érvényes, beleértve a kezdő és a lejárati időpont.
- A SAS által biztosított engedélyeket. Például a blob SAS előfordulhat, hogy adjon egy felhasználó olvasási és írási engedélyekkel, hogy a blob, de nem törli az engedélyeket.
- Nem kötelező IP-cím vagy IP-címek tartománya, amelyből az Azure Storage fogad az SAS. Például előfordulhat, hogy adja meg egy IP-címtartományt a szervezethez tartozó. Ez biztosítja, hogy egy új mértéket, az SAS a biztonságot.
- A protokoll, amelyben Azure Storage fogadja el az SAS. Ez nem kötelező paraméter használatával korlátozza a hozzáférést az ügyfelek HTTPS-en keresztül.

SAS oszthat meg tartalmakat anélkül azonnal a tárfiókkulcsokat megosztja azokat a kívánt módon teszi lehetővé. Mindig SAS-sel az alkalmazásban a biztonságos módon a tárolási erőforrások megosztását a storage-fiókkulcsok veszélyeztetése nélkül.

Közös hozzáférésű jogosultságkód kapcsolatos további információkért lásd: [a közös hozzáférésű jogosultságkódot](../storage/common/storage-dotnet-shared-access-signature-part-1.md). 

## <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés alkalmazása
Hozzáférés kezelése egy másik módja [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md) (RBAC). Az RBAC, kifejezetten szükségük van, a pontos engedélyeket ad az alkalmazottak az alapján kell tudnia és a legalacsonyabb jogosultsági biztonsági alapelveket. Túl sok engedély egy fiókot a támadók tehetők közzé. Túl kevés engedélyeket, az azt jelenti, hogy az alkalmazottak nem munkavégzéséhez hatékony. Az RBAC lehetővé teszi, hogy oldja meg a problémát azzal részletes hozzáférés-vezérlést az Azure-hoz. Ez elengedhetetlen, szervezetek által adatok elérésére vonatkozó biztonsági szabályzatok kikényszerítéséhez.

Az Azure beépített RBAC-szerepkörök használatával jogosultságok hozzárendelése a felhasználók számára. A felhő üzemeltetői, amely a storage-fiókok és a klasszikus tárfiókok kezelését klasszikus Tárfiók-közreműködő szerepkörrel kell például használja a Tárfiók-közreműködő. A felhő üzemeltetői igénylő kezelése virtuális gépek, de nem a virtuális hálózat vagy a storage-fiókot, amelyhez csatlakoznak a virtuális gépek Közreműködője szerepkörhöz felveheti őket.

Szervezetek számára, amelyek nem tesszük kötelezővé a hozzáférés-vezérlés a képességek, például az RBAC használatával is lehet jogosultságot ad a felhasználók számára a szükségesnél több jogosultsággal. Ez vezethet a biztonsági adatokat azáltal, hogy egyes felhasználók nem rendelkeznek az elsőként adatokhoz való hozzáférést.

További információ az RBAC lásd:

- [Hozzáférés kezelése az RBAC és az Azure Portal használatával](../role-based-access-control/role-assignments-portal.md)
- [Az Azure-erőforrások beépített szerepkörök](../role-based-access-control/built-in-roles.md)
- [Biztonsági útmutató az Azure Storage-hoz](../storage/common/storage-security-guide.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Ügyféloldali titkosítás értékes adatok használata
Ügyféloldali titkosítás lehetővé teszi, hogy programozott módon titkosíthatóak, mielőtt feltöltené az Azure Storage az átvitt adatokat, és programozott módon a beolvasása, az adatok visszafejtéséhez. Ez az átvitt adatok titkosítását biztosítja, de az inaktív adatok titkosítását is biztosít. Ügyféloldali titkosítás a legbiztonságosabb módszer az adatok titkosításához, de ehhez szükség szoftveres módosítja az alkalmazást, és kulcskezelés folyamatok vezetnek be.

Ügyféloldali titkosítás lehetővé teszi egyetlen szabályozhatják a titkosítási kulcsokat. Ön hozza létre, és a saját titkosítási kulcsok kezeléséhez. Ahol az Azure storage ügyféloldali kódtárának kulcsot generál egy tartalomtitkosító (CEK), amely ezt követően (titkosítva) használatával a kulcstitkosítási kulcs-(KEK)-boríték módszer azt használja. A KEK kulcsazonosítójával azonosíthatók és aszimmetrikus kulcspárt alkotnak, vagy egy szimmetrikus kulcsot és is kezelhetők helyileg vagy tárolt [Azure Key Vault](../key-vault/key-vault-whatis.md).

Ügyféloldali titkosítás a Java és a storage .NET-ügyfélkönyvtárak be van építve. Lásd: [ügyféloldali titkosítás és a Microsoft Azure Storage for Azure Key Vault](../storage/storage-client-side-encryption.md) ügyfélalkalmazások belüli titkosítását és generálása és a saját titkosítási kulcsok kezelése kapcsolatos információkat.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Inaktív adatok a Storage szolgáltatás titkosításának engedélyezése
Amikor [a Storage Service Encryption](../storage/storage-service-encryption.md) esetében engedélyezve van a File storage, az adatok titkosítása automatikusan az AES-256 titkosítás segítségével. A Microsoft kezeli az összes titkosítási, visszafejtési és kulcskezelési. Ez a funkció az LRS és a GRS redundanciatípus érhető el.

## <a name="next-steps"></a>További lépések

Ez a cikk mutatta be Azure Storage ajánlott biztonsági eljárások a PaaS webes és mobilalkalmazások védelme gyűjteménye. A PaaS üzemelő példányok védelmével kapcsolatos további tudnivalókért lásd:

- [PaaS-környezetek védelme](security-paas-deployments.md)
- [PaaS web- és mobilalkalmazások az Azure App Services használatával biztonságossá tétele](security-paas-applications-using-app-services.md)
- [Az Azure PaaS-adatbázisok védelme](security-paas-applications-using-sql.md)
