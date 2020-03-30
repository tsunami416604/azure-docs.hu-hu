---
title: PaaS-alkalmazások biztonságossá tétele az Azure Storage használatával | Microsoft dokumentumok
description: Ismerje meg az Azure Storage biztonsági gyakorlati tanácsait a PaaS-web- és mobilalkalmazások védelméhez.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 675e10101d01d831aad7652c70cbfcf320085a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70999171"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Gyakorlati tanácsok a PaaS-web- és mobilalkalmazások Azure Storage használatával történő védelméhez
Ebben a cikkben az Azure Storage biztonsági gyakorlati tanácsok a platformszolgáltatásként (PaaS) webes és mobilalkalmazások védelmére vonatkozó gyakorlati tanácsok gyűjteményét tárgyaljuk. Ezek az ajánlott eljárások az Azure-ral kapcsolatos tapasztalatainkból és az önhöz hasonló ügyfelek tapasztalataiból származnak.

Az Azure lehetővé teszi a tárterület üzembe helyezését és használatát a helyszíni, nem könnyen elérhető módon. Az Azure storage-szal viszonylag kevés erőfeszítéssel elérheti a méretezhetőség és a rendelkezésre állás magas szintjét. Nem csak az Azure Storage a Windows és linuxos Azure virtuális gépek alapja, hanem nagy elosztott alkalmazásokat is támogathat.

Az Azure Storage a következő négy szolgáltatást nyújtja: Blob storage, Table storage, Queue storage és File storage. További információ: [Bevezetés a Microsoft Azure Storage szolgáltatásba](/azure/storage/common/storage-introduction).

Az [Azure Storage biztonsági útmutató](/azure/storage/common/storage-security-guide) jatttaszt az Azure Storage és a biztonság részletes információihoz. Ez az ajánlott eljárások cikk foglalkozik magas szinten néhány fogalmak találhatók a biztonsági útmutató és a hivatkozásokat a biztonsági útmutató, valamint egyéb forrásokból, további információkért.

Ez a cikk az alábbi gyakorlati tanácsokkal foglalkozik:

- Közös hozzáférésű jogosultságkódok (SAS)
- Szerepköralapú hozzáférés-vezérlés (RBAC)
- Ügyféloldali titkosítás nagy értékű adatokhoz
- Storage Service Encryption


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Közös hozzáférésű aláírás használata tárfiókkulcs helyett
A hozzáférés-vezérlés kritikus. Az Azure Storage-hoz való hozzáférés szabályozása érdekében az Azure két 512 bites tárfiók-kulcsot (SAK) hoz létre, amikor tárfiókot hoz létre. A kulcsredundancia szintje lehetővé teszi, hogy a szokásos kulcsrotáció során elkerülje a szolgáltatás megszakításait. 

A tárolási hozzáférési kulcsok magas prioritású titkos kulcsok, és csak a tárolási hozzáférés-vezérlésért felelős személyek számára érhetők el. Ha a nem megfelelő személyek férnek hozzá ezekhez a kulcsokhoz, akkor teljes hozzáféréssel rendelkeznek a tároláshoz, és lecserélhetik, törölhetik vagy hozzáadhatják a fájlokat a tárolóhoz. Ide tartoznak a rosszindulatú programok és más típusú tartalmak is, amelyek potenciálisan veszélyeztethetik a szervezetet vagy az ügyfeleket.

Továbbra is szükség van egy módja annak, hogy hozzáférést biztosítson a tárolóban lévő objektumokhoz. Részletesebb hozzáférés biztosítása érdekében kihasználhatja a megosztott hozzáférés-aláírás (SAS) előnyeit. A SAS lehetővé teszi, hogy meghatározott objektumokat osszon meg a tárolóban egy előre meghatározott időintervallumban és adott engedélyekkel. A közös hozzáférésű aláírás lehetővé teszi a következők meghatározását:

- Az az időtartam, amely alatt a SAS érvényes, beleértve a kezdési időt és a lejárati időt.
- A SAS által megadott engedélyek. Például egy Blob SAS adhat egy felhasználó nak olvasási és írási engedélyeket, hogy a blob, de nem törli az engedélyeket.
- Egy opcionális IP-cím vagy IP-címtartomány, amelyből az Azure Storage elfogadja a SAS.A optional IP address or range of IP addresses which Azure Storage accepts the SAS. Megadhatja például a szervezethez tartozó IP-címek tartományát. Ez egy másik biztonsági intézkedés a SAS számára.
- Az a protokoll, amely en keresztül az Azure Storage elfogadja a SAS-t. Ezzel a választható paraméterrel korlátozhatja a HTTPS protokollt használó ügyfelek hozzáférését.

A SAS lehetővé teszi, hogy a tárfiók kulcsainak elosztása nélkül ossza meg a tartalmakat. Mindig a SAS használata az alkalmazásban egy biztonságos módja a tárolási erőforrások megosztásának veszélyeztetése nélkül a tárfiók kulcsait.

A megosztott hozzáférésű aláírásról a [Közös hozzáférésű aláírások használata (Közös hozzáférésű aláírások használata) (Közös hozzáférésű aláírások használata) (Közös hozzáférésű aláírások használata) (Közös hozzáférésű aláírások használata) témakörben olvashat bővebben.](/azure/storage/common/storage-dotnet-shared-access-signature-part-1) 

## <a name="use-role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés alkalmazása
A hozzáférés kezelésének másik módja a [szerepköralapú hozzáférés-vezérlés](/azure/role-based-access-control/overview) (RBAC) használata. Az RBAC segítségével a szükséges pontos engedélyek megadására összpontosíthat az alkalmazottak számára, a szükséges ismeretés és a legkisebb jogosultságbiztonsági elvek alapján. A túl sok engedély kiteheti a fiókot a támadók számára. A túl kevés engedély azt jelenti, hogy az alkalmazottak nem tudják hatékonyan elvégezni a munkájukat. Az RBAC részletes hozzáférés-kezeléssel oldja meg a problémát az Azure-hoz. Ez elengedhetetlen azoknak a szervezeteknek, amelyek az adathozzáférés biztonsági házirendjeit szeretnék érvényesíteni.

Az Azure beépített RBAC-szerepkörei használatával jogosultságokat rendelhet a felhasználókhoz. Például használja a Storage Account Contributor a felhőbeli operátorok, amelyek a tárfiókok kezelésére és a klasszikus tárfiók közreműködői szerepkör a klasszikus tárfiókok kezeléséhez. A felhő-operátorok, amelyek nek kell kezelni a virtuális gépek, de nem a virtuális hálózat vagy tárfiók, amelyhez kapcsolódnak, hozzáadhatja őket a Virtuálisgép közreműködő szerepkör.

Azok a szervezetek, amelyek nem kényszerítik ki az adatelérés-vezérlést olyan képességek használatával, mint például az RBAC, a felhasználók számára szükségesnél több jogosultságot adhatnak. Ez adatsérüléshez vezethet azáltal, hogy egyes felhasználók számára lehetővé teszi a hozzáférést azokhoz az adatokhoz, amelyeket eleinte nem kellett volna.

Ha többet szeretne megtudni az RBAC-ról, olvassa el a következő témakört:

- [Hozzáférés kezelése az RBAC és az Azure Portal használatával](/azure/role-based-access-control/role-assignments-portal)
- [Beépített szerepkörök Azure-erőforrásokhoz](/azure/role-based-access-control/built-in-roles)
- [Az Azure Storage biztonsági útmutatója](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Ügyféloldali titkosítás használata nagy értékű adatokhoz
Az ügyféloldali titkosítás lehetővé teszi, hogy az Azure Storage-ba való feltöltés előtt programozott módon titkosítsa az átvitel alatt lévő adatokat, és programozott módon visszafejtse az adatokat azok beolvasásakor. Ez biztosítja az adatok titkosítását az átvitel során, de biztosítja az inaktív adatok titkosítását is. Az ügyféloldali titkosítás az adatok titkosításának legbiztonságosabb módja, de programozott módosításokat igényel az alkalmazásban, és kulcskezelési folyamatokat kell életbe léptetni.

Az ügyféloldali titkosítás azt is lehetővé teszi, hogy kizárólagos anamtalán irányítsa a titkosítási kulcsokat. Létrehozhatja és kezelheti saját titkosítási kulcsait. Egy borítéktechnikát használ, ahol az Azure storage-ügyfélkódtár létrehoz egy tartalomtitkosítási kulcsot (CEK), amely et aztán a kulcs titkosítási kulcs (KEK) használatával csomagolja (titkosítja). A KEK-et egy kulcsazonosító azonosítja, és lehet aszimmetrikus kulcspár vagy szimmetrikus kulcs, és helyileg vagy az [Azure Key Vaultban](/azure/key-vault/key-vault-overview)tárolható.

Az ügyféloldali titkosítás be van építve a Java és a .NET tárolóügyfél-kódtárakba. Az [ügyféloldali titkosítás és az Azure Key Vault for Microsoft Azure Storage](/azure/storage/common/storage-client-side-encryption) című témakörben talál információt az ügyfélalkalmazásokon belüli adatok titkosításáról, valamint a saját titkosítási kulcsok létrehozásáról és kezeléséről.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Tárolószolgáltatás titkosításának engedélyezése az inaktív adatokhoz
Ha a [Storage Service Encryption](/azure/storage/common/storage-service-encryption) for File Storage engedélyezve van, az adatok automatikusan titkosítva lesznek az AES-256 titkosítással. A Microsoft kezeli az összes titkosítást, visszafejtést és kulcskezelést. Ez a funkció LRS- és GRS-redundanciatípusokesetén érhető el.

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta az Azure Storage biztonsági gyakorlati tanácsok a PaaS webes és mobilalkalmazások védelmére. A PaaS-telepítések védelméről a következő témakörökben olvashat bővebben:

- [PaaS-környezetek védelme](paas-deployments.md)
- [PaaS webes és mobilalkalmazások védelme az Azure App Services használatával](paas-applications-using-app-services.md)
- [PaaS-adatbázisok védelme az Azure-ban](paas-applications-using-sql.md)
