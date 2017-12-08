---
title: "Az Azure Storage szolgáltatás titkosítási ügyfél használatával felügyelt kulcsok Azure Key Vault |} Microsoft Docs"
description: "Az Azure Blob Storage szolgáltatás oldalán titkosítani, ha az adatok tárolása az Azure Storage szolgáltatás titkosítási szolgáltatás segítségével, és visszafejteni, ha a felhasználói adatok beolvasása kulcsok kezelése."
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Felügyelt felhasználói kulcsok használata az Azure Key Vault Storage szolgáltatás titkosítási

Microsoft Azure a elkötelezett az Ön és az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalások megvédeni.  Az inaktív adatok védelméhez egyik módja Storage szolgáltatás titkosítási (SSE), amely titkosítja az adatokat, történő írásakor, tárolási és automatikusan visszafejti az adatok beolvasása közben azt használja. A titkosítási és visszafejtési automatikus, teljes mértékben transzparens, és használja 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), a legerősebb blokk egyik Rejtjelek érhető el.

A Microsoft által felügyelt titkosítási kulcsok használatával SSE, vagy használhatja a saját titkosítási kulccsal. A cikk ismerteti az utóbbi. További információ a Microsoft által felügyelt kulcsokkal, vagy SSE általában: [Storage szolgáltatás titkosítási az inaktív adatok](../storage-service-encryption.md).

Adja meg a nem tudják használni a saját titkosítási kulccsal, a Blob Storage SSE integrálva van Azure Key Vault (AKV). Létrehozhat saját titkosítási kulccsal, és tárolja őket a AKV, vagy AKV tartozó API-k segítségével készítése a titkosítási kulcsokat. Nem csak AKV lehetővé teszi a kezelhetik és szabályozhatják a kulcsokat, emellett lehetővé teszi a kulcs használatának naplózása. 

Miért érdemes a saját kulcsokat hoz létre? Ez további rugalmasságot biztosít, lehetővé téve a létrehozása, elforgatása, tiltsa le, és adja meg a hozzáférés-vezérlést. Lehetővé teszi az adatok védelme érdekében használt titkosítási kulcsok naplózása.

## <a name="sse-with-customer-managed-keys-preview"></a>Az ügyfél felügyelt kulcsoknál előzetes verziójú SSE

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Ez a funkció használatához hozzon létre egy új tárfiókot kell. Hozhat létre egy új kulcstartó és a kulcs vagy egy meglévő kulcstároló és a kulcs használhatja. A tárfiók és a key vault ugyanabban a régióban kell lennie, de különböző előfizetésekhez el.

Az előzetes részt, lépjen kapcsolatba a [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com). Egy hivatkozásra részt venni az előzetes kiadásban lesz elérhető.

További tudnivalókért tekintse meg a [gyakran ismételt kérdések](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest).

> [!IMPORTANT]
> Regisztrálnia kell az előzetes ebben a cikkben ismertetett lépések előtt. Előzetes verzióhoz való hozzáférés nélkül nem lesz a portálon a funkció engedélyezése érdekében.

## <a name="getting-started"></a>Első lépések
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>1. lépés: [hozzon létre egy új tárfiókot](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>2. lépés: Engedélyezze titkosítás
A tárolási fiók az SSE engedélyezheti a [Azure-portálon](https://portal.azure.com). A beállítások panelen a tárfiók a Blob szolgáltatás szakaszban keresse meg a következő ábrán látható, és kattintson a titkosítás.

![A titkosítási beállítással portál ábrázoló képernyőfelvétel](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*Blob szolgáltatás SSE engedélyezése*

Ha azt szeretné, hogy programozott módon engedélyezze vagy tiltsa le a tárolás titkosítását egy tárfiókon, használja a [Azure Storage erőforrás szolgáltató REST API felülete](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN), a [Storage erőforrás szolgáltató ügyféloldali kódtára a .NET](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN), [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0), vagy a [Azure CLI](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli).

Ezen a képernyőn Ha nem látja a "saját kulcs használata" jelölőnégyzet azt nem jóváhagyott az előzetes verziójára. Az e-mail küldése [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) és jóváhagyás-igénylést.

![Titkosítási betekintő portál képernyőfelvétel](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

Alapértelmezés szerint az SSE Microsoft által felügyelt kulcsokat használ. A saját kulcsok használatához jelölje be a jelölőnégyzetet. Majd adja meg a kulcs URI, vagy válassza ki a kulcs és a Key Vault a választó.

## <a name="step-3-select-your-key"></a>3. lépés: Válassza ki a kulcs

![Portál ábrázoló képernyőfelvétel titkosítás használata a saját kulcs lehetőséggel](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![Portál képernyőfelvétel: a titkosítási kulcs uri-beállítás megadása](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Ha a tárfiók nincs hozzáférése a Key Vault, futtathatja az Azure Powershell hozzáférést biztosít a szükséges kulcstartóhoz storage-fiókok a következő parancsot.

![A hozzáférés megtagadva a kulcstartót portál képernyőfelvétel](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Ugrás az Azure Key Vault az Azure portálon, és a tárfiók való hozzáférés biztosítása szerint is engedélyezheti a hozzáférést az Azure-portálon.

## <a name="step-4-copy-data-to-storage-account"></a>4. lépés: Adatok másolása storage-fiók
Ha szeretné, hogy a titkosított adatok át az új tárfiók, tekintse meg a [lépés 3 az első lépések a Storage szolgáltatás titkosítási az inaktív adatok](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account).

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>5. lépés: A titkosított adatok állapotának lekérdezése
A titkosított adatok állapotának lekérdezése, tekintse meg [lépés 4 az első lépések a Storage szolgáltatás titkosítási az inaktív adatok](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data).

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Gyakori kérdések Storage szolgáltatás titkosítási az inaktív adatok
**K: használom a prémium szintű storage; Használhatok SSE felügyelt ügyfél kulcsokkal?**

V: Igen, a Microsoft által felügyelt SSE és az ügyfelek felügyelt kulcsok szabványos tárolás és a prémium szintű storage egyaránt támogatott. 

**K: hozható létre új tárfiókok az SSE engedélyezve van az Azure PowerShell és az Azure parancssori felület használatával felügyelt ügyfél kulcsokkal?**

V: Igen.

**K: hogyan sokkal több does Azure tárolási költségű SSE ügyféllel felügyelt kulcsok Ha engedélyezve van?**

V: nincs az Azure Key Vault használatával költsége. További részletekért látogasson el [Key Vault díjszabása](https://azure.microsoft.com/en-us/pricing/details/key-vault/). Nincs SSE használatára vonatkozó további költség nélkül.

**K: visszavonja a titkosítási kulcsokat a hozzáférést?**

A: visszavonhatja a hozzáférést Igen, tetszőleges időpontban. Többféleképpen is lehet visszavonni a hozzáférést a kulcsokat. Tekintse meg [Azure Key Vault PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) és [Azure Key Vault CLI](https://docs.microsoft.com/en-us/cli/azure/keyvault) további részleteket. Visszavonni a hozzáférési gyakorlatilag letiltja való hozzáférést a tárfiókban lévő összes BLOB, a fiók titkosítási kulcs Azure Storage nem érhetők el.

**K: hozható létre a tárfiók és a kulcs másik régióban?**

V: nem, a tárfiók és a kulcs tárolóbeli/kulccsal kell ugyanabban a régióban kell. 

**K: engedélyezhető az SSE felügyelt ügyfél kulccsal rendelkező a storage-fiók létrehozása közben?**

V: nem. A storage-fiók létrehozása közben engedélyezésekor SSE Microsoft által felügyelt kulcsok csak használhatja. Ha azt szeretné, az ügyfél által felügyelt kulcsokat kíván használni, frissítenie kell a tárfiók tulajdonságai. Programozott frissítése a tárfiók REST- vagy a storage ügyfélkódtáraival egyikét használja, vagy frissíteni a tárfiók tulajdonságai-fiók létrehozása után az Azure portál használatával.

**K: letiltani titkosítás közben SSE ügyféllel felügyelt kulcsok?**

V: nem, akkor titkosítás nem tiltható le közben SSE ügyféllel felügyelt kulcsok. Tiltsa le a titkosítást, át kell váltania a Microsoft által felügyelt kulcsokkal. Ehhez az Azure-portálon vagy a PowerShell használatával.

**K: SSE alapértelmezés szerint engedélyezve van, egy új tárfiók létrehozásakor?**

V: SSE; alapértelmezés szerint nincs engedélyezve az Azure portál segítségével engedélyezheti azt. Ez a funkció a tárolási erőforrás-szolgáltató REST API használatával programozott módon is engedélyezhető. 

**K: nem engedélyezhető a titkosítás a storage-fiókom.**

V: az azt egy erőforrás-kezelő tárfiókot? Klasszikus tárfiókok nem támogatottak. Az ügyfél által felügyelt kulcsok SSE csak az újonnan létrehozott erőforrás-kezelő storage-fiókok is engedélyezhető.

**K: van SSE ügyféllel felügyelt csak meghatározott régióiba engedélyezett kulcsok?**

V: SSE csak bizonyos régiókban a Blob-tároló ebben az előzetes verzióban érhető el. E-mailek [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) kereséséhez a rendelkezésre állás és a részletek a Preview-ban. 

**K: hogyan do I kapcsolatfelvételre Ha I problémák merülnek fel, vagy visszajelzést szeretne biztosítani?**

V: kapcsolattartási [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) a Storage szolgáltatás titkosítási okozó problémákat. 

## <a name="next-steps"></a>Következő lépések

*   További információk az átfogó készletét biztonsági funkciókat nyújtanak, amelyek segítségével a fejlesztők olyan biztonságos alkalmazásokat hozhat létre, lásd: a [tárolási biztonsági útmutatója](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide).
*   Áttekintés az Azure Key Vault kapcsolatos információkért lásd: [Mi az Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)?
*   Ismerkedés az Azure Key Vault, lásd: [Ismerkedés az Azure Key Vault](../../key-vault/key-vault-get-started.md).
