---
title: "Az inaktív adatok Azure Storage szolgáltatás titkosítási |} Microsoft Docs"
description: "Az Azure Storage szolgáltatás titkosítási szolgáltatás segítségével az Azure Blob Storage szolgáltatás oldalán titkosítani, ha az adatok tárolása, és visszafejteni az adatok beolvasása közben."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: d04752c92218d17b4e90bb03a2ed2ac5a0a11b93
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Az Azure Storage szolgáltatás inaktívadat-titkosítása
Az Azure Storage szolgáltatás titkosítási (SSE) inaktív adatok segítségével és az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalások megvédeni. Ezzel a funkcióval az Azure Storage automatikusan titkosítja az adatokat a tárolás előtt, és visszafejti őket a lekérés előtt. A titkosítás, visszafejtés, és kulcskezelés teljes mértékben átlátható a felhasználók számára.

A következő szakaszokban részletes útmutatás a Storage szolgáltatás titkosítási funkciók, valamint a támogatott forgatókönyveket, és a felhasználói élmény.

## <a name="overview"></a>Áttekintés
Az Azure Storage biztonsági képességeket, amelyek együtt lehetővé teszik a fejlesztők számára a biztonságos alkalmazások széles választékát nyújtja. Adatok védve legyenek az alkalmazás és az Azure közötti átvitel során használatával [ügyféloldali titkosítás](../storage-client-side-encryption.md), HTTPs és SMB 3.0-s. Storage szolgáltatás titkosítási titkosítását, kezelési titkosítási, visszafejtési és kulcskezelés teljesen átlátható módon biztosít. Összes adat titkosítva van, 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), a legerősebb blokk egyik Rejtjelek érhető el.

SSE működik az Azure Storage írása, és az Azure Blob, fájl, Table és Queue Storage használható adatok titkosításával. Az alábbi működik:

* Standard szintű Storage: Általános célú tárfiókok létrehozása blobokat, fájl, Table és Queue storage és a Blob storage-fiókok
* Prémium szintű Storage 
* A klasszikus és ARM tárolási fiókok alapértelmezés szerint engedélyezve 
* Minden redundancia szintek (LRS, zrs-t, Georedundáns, RA-GRS)
* Az Azure Resource Manager tárfiókok (de nem klasszikus) 
* Minden egyes.

További tudnivalókért tekintse meg a gyakran ismételt kérdések.

A tárfiók Storage szolgáltatás titkosítási beállítások megtekintéséhez jelentkezzen be a [Azure-portálon](https://portal.azure.com) , és válasszon egy tárfiókot. A beállítások panelen keresse meg a Blob szolgáltatás szakasz ezen a képernyőfelvételen látható módon, és kattintson a titkosítás.

![A titkosítási beállítással portál ábrázoló képernyőfelvétel](./media/storage-service-encryption/image1.png)
<br/>*1. ábra: SSE engedélyezése a Blob szolgáltatás (1. lépés)*

![A titkosítási beállítással portál ábrázoló képernyőfelvétel](./media/storage-service-encryption/image3.png)
<br/>*2. ábra: (1. lépés) Fájlszolgáltatás SSE engedélyezése*

A titkosítási beállítás gombra kattintva engedélyezheti vagy letilthatja a Storage szolgáltatás titkosítási.

![Képernyőfelvétel: portál-titkosítás tulajdonságai](./media/storage-service-encryption/image2.png)
<br/>*3. ábra: BLOB SSE engedélyezése és a szolgáltatás (2. lépés)*

## <a name="encryption-scenarios"></a>Titkosítási forgatókönyvek
Tárolási szolgáltatás engedélyezve van a tárolási fiók szintjén. Alapértelmezett beállításként engedélyezve van az összes szolgáltatáshoz. Az alábbi forgatókönyvet támogatja:

* A klasszikus és Resource Manager-fiókok Blob, a fájl, a tábla és a Queue Storage Encryption.

SSE rendelkezik a következő korlátozások vonatkoznak:

* Meglévő adatok - SSE csak titkosítja az újonnan létrehozott adatokat, a titkosítás engedélyezése után. Ha például létrehoz egy új erőforrás-kezelő tárfiókot, de ne kapcsolja be a titkosítás, és majd blobok és archivált VHD feltöltése a tárfiók, és kapcsolja be SSE, ezeket a blobok nem lesznek titkosítva kivéve, ha a rendszeren, vagy másolja.
* Piactér támogatása – a piactér a létrehozott virtuális gépek titkosításának engedélyezése a [Azure-portálon](https://portal.azure.com), PowerShell és az Azure parancssori felület. A virtuális merevlemez alapjául szolgáló lemezképhez marad titkosítatlan; azonban bármilyen végre, miután a virtuális gép rendelkezik hoz létre írás lesz titkosítva.

## <a name="getting-started"></a>Első lépések
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>1. lépés: [hozzon létre egy új tárfiókot](../storage-create-storage-account.md).
### <a name="step-2-verify-encryption"></a>2. lépés: Ellenőrizze a titkosítás.
Titkosítás segítségével ellenőrizheti a [Azure-portálon](https://portal.azure.com).

> [!NOTE]
> Ha meg szeretné programozott módon ellenőrizze, hogy Storage szolgáltatás titkosítási egy tárfiókot, használhatja a [Azure Storage erőforrás szolgáltató REST API felülete](https://msdn.microsoft.com/library/azure/mt163683.aspx), a [Storage erőforrás szolgáltató ügyféloldali kódtára a .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx) , [Azure PowerShell](/powershell/azureps-cmdlets-docs), vagy a [Azure CLI](../storage-azure-cli.md).
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>3. lépés: Adatok másolása storage-fiók
Amennyiben az SSE engedélyezve van a tárfiókon, tárolási fiók írt adatok lesz titkosítva. Csak azok a rendszer újraírja, hogy a tárfiók már található adatokat nem lesznek titkosítva. Egy tároló adatok átmásolhatja a másikba, győződjön meg arról, hogy az előző adatok titkosítva legyenek. Ehhez használhatja a következő eszközök bármelyikével. Ez történik, ugyanazt a fájlt, Table és Queue Storage is.

#### <a name="using-azcopy"></a>AzCopy használatával
AzCopy készült adatok másolása, és az egyszerű parancsokkal optimális teljesítménnyel Microsoft Azure Blob, a fájl és a Table storage egy Windows parancssori segédprogram. Ezzel a blobok vagy a fájlok másolása egy tárfiókot, amelyen engedélyezve van SSE egy másikat. 

További információkért látogasson el [adatátvitel az AzCopy parancssori segédprogram a](storage-use-azcopy.md).

#### <a name="using-smb"></a>Az SMB-
Az Azure Files kínál a felhőben, szabványos SMB protokollt használó fájlmegosztások. Egy fájlmegosztást csatlakoztathatnak egy ügyfél a helyszínen vagy az Azure-ban. Ha csatlakoztatva, és eszközöket, például a Robocopy használatával lehet fájlokat másolja át az Azure fájlmegosztások. További információkért lásd: [csatlakoztatásáról Azure fájlmegosztás Windows rendszeren](../files/storage-how-to-use-files-windows.md) és [Azure fájlmegosztás csatlakoztatásáról Linux](../files/storage-how-to-use-files-linux.md).


#### <a name="using-the-storage-client-libraries"></a>A Storage Ügyfélkódtáraival használatával
Blob vagy a fájl adatainak másolhatja és a blob-tároló vagy a Storage Ügyfélkódtáraival, beleértve a .NET, C++, Java, Android, Node.js, PHP, Python és Ruby széles skáláját alkalmazó tárfiókok között.

További információkért látogasson el a [az Azure Blob storage .NET használatának első lépései](../blobs/storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>A Tártallózó használatával
Tárfiókok létrehozása, feltöltése és adatok letöltése, megtekintheti a BLOB tartalmát és haladjon végig a könyvtárak Tártallózóval segítségével. Ezek egyikét használhatja a blobok feltöltése a tárfiókhoz a titkosítás engedélyezve van. Az egyes tártallózók is másolhatja adatok meglévő blob-tároló egy másik tárolóhoz a storage-fiók vagy egy új tárfiókot, amely rendelkezik az SSE engedélyezve van.

További információkért látogasson el [Azure Tártallózók](../storage-explorers.md).

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>4. lépés: A titkosított adatok állapotának lekérdezése
A Storage ügyféloldali kódtáraknál frissített verziója telepítve van, amely lehetővé teszi határozza meg, ha azok titkosítottak vagy nem egy objektumot állapotának lekérdezése. Jelenleg ez csak a Blob storage érhető el. A fájltároló is lehetnek a programba. 

Időközben hívása [fiók tulajdonságok beolvasása](https://msdn.microsoft.com/library/azure/mt163553.aspx) ellenőrizze, hogy a storage-fiók rendelkezik-e a titkosítás engedélyezett, vagy tekintse meg a tárfiók tulajdonságai az Azure portálon.

## <a name="encryption-and-decryption-workflow"></a>Titkosítás és visszafejtés munkafolyamat
A titkosítási/visszafejtési munkafolyamat rövid leírása itt található:

* Engedélyezve van a tárfiók.
* Ha az ügyfél új adatokat ír (Blob PUT PUT blokk PUT lapon PUT fájl stb.) a Blob vagy a fájl tárolási; minden egyes van titkosítva, 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), a legerősebb blokk egyik Rejtjelek érhető el.
* Ha a felhasználói adatokat (a Blob LEKÉRÉSE, stb.) hozzáférésre van szüksége, adatai automatikusan visszafejtett a felhasználó számára való visszaküldés előtt.
* Védelem le van tiltva, ha új írási műveletek többé nem lesznek titkosítva, és a meglévő titkosított adatok titkosítva maradnak, amíg a felhasználó által írni. Titkosítás engedélyezve van, amíg a Blob vagy a fájl írási műveletek lesz titkosítva. A felhasználó a tárfiók titkosítás engedélyezése vagy tiltása váltása nem változik az adatok állapotát.
* Minden titkosítási kulcs tárolása, titkosított, és a Microsoft által felügyelt.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Gyakori kérdések Storage szolgáltatás titkosítási az inaktív adatok
**K: van egy létező klasszikus storage-fiókot. Engedélyezhető az SSE rajta?**

V: SSE összes tárfiókok (klasszikus és Resource Manager storage-fiókok) alapértelmezés szerint engedélyezve van.

**K: hogyan is titkosítani a klasszikus tárfiókban lévő adatokat?**

A: az új adatok titkosítással alapértelmezés szerint engedélyezve van, automatikusan titkosítja a tároló szolgáltatást. 

Is hozzon létre egy új erőforrás-kezelő tárfiókot és másolhatja az összes használata esetén az adatok [AzCopy](storage-use-azcopy.md) hagyományos tárolási meglévő a Resource Manager újonnan létrehozott tárfiók fiókjából. 

Választhatja azt is, a klasszikus tárfiók át egy erőforrás-kezelő tárfiókot. Ez a művelet azonnali, akkor a fiók típusának módosítása, de nem érinti a meglévő adatokat. Bármely új adatokat csak a titkosítás engedélyezése után lesz titkosítva. További információkért lásd: [Platform támogatott áttelepítési az IaaS-erőforrásokra a klasszikus az erőforrás-kezelő](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/). Vegye figyelembe, hogy ez csak a Blob vagy a fájl szolgáltatás támogatott.

**K: van egy meglévő Resource Manager storage-fiókot. Engedélyezhető az SSE rajta?**

V: SSE alapértelmezés szerint az összes meglévő Resource Manager storage-fiók engedélyezve van. Ez támogatott BLOB, a táblák, a fájl és a várólista tárolási. 

**K: szeretnék a Resource Manager meglévő tárfiókot az aktuális adatok titkosítása?**

V: az SSE összes storage-fiókok – klasszikus és Resource Manager alapértelmezés szerint engedélyezett a storage-fiókok. Azonban már jelen adatok nem lesznek titkosítva. Meglévő adatok titkosítása, másolja őket egy másik nevet vagy egy másik tárolóban, és távolítsa el a titkosítatlan verziója. 

**K: használom a prémium szintű storage; használható SSE?**

A: SSE Igen, Standard szintű tárolást és a prémium szintű Storage esetén támogatott.  Prémium szintű Storage esetében a szolgáltatás nem támogatott.

**Kérdés: Ha engedélyezett SSE hozok létre egy új tárfiókot, majd egy új virtuális gép létrehozása, hogy a storage-fiók esetén, hogy a virtuális gép titkosított közepét does?**

V: Igen. A létrehozott lemezek, amelyek az új tárfiók lesz titkosítva, mindaddig, amíg a létrehozásuk után az SSE engedélyezve van. Ha a virtuális gép létrehozása Azure piactéren használatával, a virtuális merevlemez alapjául szolgáló lemezképhez marad titkosítatlan; azonban bármilyen végre, miután a virtuális gép rendelkezik hoz létre írás lesz titkosítva.

**K: hozható létre új tárfiókok az SSE engedélyezve van az Azure PowerShell és az Azure parancssori felület használatával?**

V: SSE alapértelmezés szerint engedélyezve van a bármely (klasszikus és Resource Manager-fiókok) storage-fiók létrehozásának időpontjában. Ellenőrizheti a fióktulajdonságok Azure PowerShell és az Azure parancssori felület használatával.

**K: hogyan sokkal nem költségeket, Azure Storage Ha SSE engedélyezve van?**

V: nincs további költség nélkül.

**K: kezelő a titkosítási kulcsokat?**

A: a Microsoft által felügyelt a kulcsokat.

**K: használhatok saját titkosítási kulcsokat?**

V: jelenleg is dolgozunk az ügyfelek számára a saját titkosítási kulcsok állapotba képességek biztosítása.

**K: visszavonja a titkosítási kulcsokat a hozzáférést?**

V: jelenleg nem; a kulcsok teljes mértékben a Microsoft által felügyelt.

**K: SSE alapértelmezés szerint engedélyezve van, egy új tárfiók létrehozásakor?**

A: Igen Microsoft Managed kulcsokkal SSE alapértelmezés szerint engedélyezve van az összes tárfiók - Azure Resource Manager és klasszikus tárfiókokkal. Az összes szolgáltatás, valamint - Blob, Table, várólistára és fájltárolás engedélyezve van.

**K: hogyan eltér a Azure Disk Encryption?**

V: Ez a funkció az Azure Blob storage-adatok titkosítására szolgál. Az Azure Disk Encryption operációsrendszer- és adatlemezek az infrastruktúra-szolgáltatási virtuális gépek titkosítására szolgál. További részletekért tekintse meg a [tárolási biztonsági útmutatója](../storage-security-guide.md).

**K: Mit tegyek, ha engedélyezve van-e az SSE, és ezután I engedélyezése az Azure Disk Encryption a lemezeken?**

A: Ez zökkenőmentesen működnek. Mindkét módszer által az adatok titkosítva lesznek.

**K: a tárfiók földrajzi redundantly replikálható be van állítva. Ha engedélyezve van a SSE, saját redundáns példány is titkosítva lesznek?**

V: Igen, a tárfiók összes másolatát titkosított, és – helyileg redundáns tárolás (LRS), zóna-redundáns tárolás (ZRS), Georedundáns tárolás (GRS) és írásvédett Georedundáns tárolás (RA-GRS) – összes redundancia beállítások támogatottak.

**K: I titkosítás nem tiltható le a storage-fiókom.**

Válasz: alapértelmezés szerint engedélyezve van a titkosítás, és a tiltsa le a titkosítást a tárfiók nem rendelkeznek. 

**K: SSE csak engedélyezett meghatározott régióiba?**

V: az SSE szolgáltatások minden területen érhető el. 

**K: hogyan do I kapcsolatfelvételre Ha I problémák merülnek fel, vagy visszajelzést szeretne biztosítani?**

V: kapcsolattartási [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) a Storage szolgáltatás titkosítási okozó problémákat.

## <a name="next-steps"></a>További lépések
Az Azure Storage biztonsági képességeket, amelyek együtt lehetővé teszik a fejlesztők számára a biztonságos alkalmazások széles választékát nyújtja. További részletekért látogasson el a [tárolási biztonsági útmutatója](../storage-security-guide.md).
