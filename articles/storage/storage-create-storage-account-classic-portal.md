---
title: "Tárfiókok létrehozása, kezelése vagy törlése a klasszikus Azure portálon | Microsoft Docs"
description: "Az Azure portálon létrehozhat egy új tárfiókot, kezelheti a fiók hozzáférési kulcsait, vagy törölheti a tárfiókokat. Ismerje meg a standard és a prémium szintű tárfiókokat."
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 5e4f4360-3f81-4d63-a0b1-e7771b67af11
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 879e199f0c712a46b89a686d6a3dd8dd877821e4


---
# <a name="about-azure-storage-accounts"></a>Tudnivalók az Azure Storage-fiókokról
[!INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

[!INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Áttekintés
Az Azure Storage-fiókok hozzáférést biztosítanak az Azure Storage Azure Blob, Queue, Table és File szolgáltatásaihoz. A tárfiók biztosítja az egyedi névteret Azure Storage-adatobjektumaihoz. Alapértelmezés szerint a fiókban tárolt adatok csak Ön, azaz a fiók tulajdonosa számára érhetőek el.

Kétféle tárfióktípus létezik:

* A standard szintű tárfiók a Blob, Table, Queue és a File tárolási szolgáltatást foglalja magában.
* A prémium szintű tárfiók jelenleg csak az Azure virtuális gépek lemezeit támogatja. A Premium Storage részletesebb áttekintéséért lásd: [Premium Storage: High-performance Storage for Azure Virtual Machine Workloads](storage-premium-storage.md) (Premium Storage: Nagy teljesítményű tárterület az Azure virtuális gépek számítási feladataihoz).

## <a name="storage-account-billing"></a>Tárfiókok számlázása
Az Azure Storage használatáért a tárfiók alapján kell fizetni. A tárolási költségeket négy tényező határozza meg: a tárolási kapacitás, a replikálási séma, a tárolási tranzakciók és a kimenő adatforgalom.

* A tárolási kapacitás azt jelzi, hogy tárfiókja mekkora részét használja adatok tárolására. Az adatok tárolásának költségét az határozza meg, hogy mennyi adatot tárol, és azok replikálása hogyan történik.
* A replikálás határozza meg, hogy adatait egyszerre hány példányban és hol tárolja.
* A tranzakciók az összes olvasási és írási műveletet jelentik az Azure Storage szolgáltatásban.
* A kimenő adatforgalom az adott Azure-régiókból kifelé irányuló adatátvitelt jelenti. Amikor a tárfiókban lévő adatokat egy olyan alkalmazás használja, amely nem ugyanabban a régióban fut, függetlenül attól, hogy felhőszolgáltatásról vagy egyéb típusú alkalmazásról van szó, a kimenő adatforgalom díjköteles. (Az Azure szolgáltatások esetében adatait és szolgáltatásait azonos adatközpontba csoportosítva csökkentheti vagy elkerülheti a kimenő adatforgalom díját.)  

[Az Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage) lap részletes információkat biztosít a tárolási kapacitással, a replikálással és a tranzakciókkal kapcsolatban. Az [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/) lap tartalmazza a kimenő adatforgalommal kapcsolatos részletes díjszabási információkat.

A tárfiókok kapacitási és teljesítménycéljaival kapcsolatos további információk: [Azure Storage Scalability and Performance Targets](storage-scalability-targets.md) (Az Azure Storage méretezhetőségi és teljesítménycéljai).

> [!NOTE]
> Azure virtuális gép létrehozásakor a rendszer automatikusan létrehoz egy tárfiókot az Ön számára a telepítés helyén, ha nem rendelkezik még tárfiókkal azon a helyen. Így nem szükséges a tárfiók létrehozásának alábbi lépéseit végrehajtania a virtuális gép lemezeihez. A tárfiók neve a virtuális gép nevén alapul. További részletek: [Azure Virtual Machines – dokumentáció](https://azure.microsoft.com/documentation/services/virtual-machines/).
> 
> 

## <a name="create-a-storage-account"></a>Create a storage account
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. Kattintson a lap alján a tálcán található **Új** gombra. Válassza a **Data Services** | **Tárolás** lehetőséget, majd kattintson a **Gyorslétrehozás** elemre.
   
    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)
3. Az **URL-cím** mezőben adjon meg egy nevet a tárfiók számára.
   
   > [!NOTE]
   > A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
   > 
   > A tárfiók nevének egyedinek kell lennie az Azure rendszerben. A klasszikus Azure portál jelzi, ha a választott tárfióknév már használatban van.
   > 
   > 
   
    Lásd az alábbiakban a [Tárfiókvégpontok](#storage-account-endpoints) című részt azzal kapcsolatban, hogy a rendszer hogyan használja a tárfiók nevét az objektumok címzéséhez az Azure Storage-ban.
4. A **Hely/affinitáscsoport** részben válasszon egy olyan helyet a tárfiók számára, amely közel található Önhöz vagy az ügyfeleihez. Ha a tárfiókban lévő adatokat egy másik Azure szolgáltatás, például egy Azure virtuális gép vagy felhőszolgáltatás használja majd, érdemes lehet egy affinitáscsoportot választania a listából, hogy tárfiókját egy adatközpontba csoportosítsa a többi használt Azure szolgáltatással a teljesítmény növelése és a költségek csökkentése érdekében.
   
    Ügyeljen arra, hogy az affinitáscsoportot a tárfiók létrehozásakor kell kiválasztania. Meglévő tárfiókot nem helyezhet át egy affinitáscsoportba. Az affinitáscsoportokkal kapcsolatos további információkért lásd az alábbiakban a [Szolgáltatások közös elhelyezése affinitáscsoporttal](#service-co-location-with-an-affinity-group) című szakaszt.
   
   > [!IMPORTANT]
   > Annak meghatározásához, hogy az előfizetéséhez melyik helyek elérhetőek, meghívhatja [Az összes erőforrás-szolgáltató listázása](https://msdn.microsoft.com/library/azure/dn790524.aspx) műveletet. A szolgáltatók a PowerShellből való listázása a [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx) meghívásával lehetséges. A .NET-keretrendszerből használja a ProviderOperationsExtensions osztály [List](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) metódusát.
   > 
   > Továbbá tekintse meg [Az Azure régiói](https://azure.microsoft.com/regions/#services) című lapot azzal kapcsolatban, hogy melyik régióban mely szolgáltatások érhetőek el.
   > 
   > 
5. Ha több Azure-előfizetéssel rendelkezik, megjelenik az **Előfizetés** mező. Az **Előfizetés** mezőben adja meg azt az Azure-előfizetést, amelyikkel a tárfiókot használni kívánja.
6. A **Replikáció** mezőben válassza ki a tárfiók replikálásának kívánt szintjét. Az ajánlott replikációs beállítás a georedundáns replikáció, amely maximális tartósságot biztosít adatainak. Az Azure Storage replikálási beállításaival kapcsolatos további részletek: [Azure Storage replication](storage-redundancy.md) (Az Azure Storage replikációja).
7. Kattintson a **Tárfiók létrehozása** lehetőségre.
   
    A tárfiók létrehozása eltarthat néhány percig. A folyamat állapotának ellenőrzéséhez figyelje az értesítéseket a klasszikus Azure portál alján. Miután a rendszer létrehozta a tárfiókot, az új tárfiók **online** állapotú, és használatra kész.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)

### <a name="storage-account-endpoints"></a>Tárfiókvégpontok
Az Azure Storage szolgáltatásban tárolt minden egyes objektum egyedi URL-címmel rendelkezik. A tárfiók neve képezi a cím altartományát. Az altartomány és a tartomány a szolgáltatásra jellemző neve együtt alkotnak egy *végpontot* a tárfiók számára.

Ha például a tárfiók neve *mystorageaccount*, a tárfiókhoz tartozó alapértelmezett végpontok a következők:

* Blob szolgáltatás: http://*mystorageaccount*.blob.core.windows.net
* Table Service: http://*mystorageaccount*.table.core.windows.net
* Queue szolgáltatás: http://*mystorageaccount*.queue.core.windows.net
* File szolgáltatás: http://*mystorageaccount*.file.core.windows.net

A tárfiók végpontjait a tárterület irányítópultján tekintheti meg a [klasszikus Azure portálon](https://manage.windowsazure.com) a fiók létrehozását követően.

A tárfiókban lévő objektumok eléréséhez szükséges URL-cím az objektum tárfiókbeli helyének a végponthoz való hozzáfűzésével épül fel. Például egy blobcím formátuma lehet a következő: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Emellett konfigurálhat egy egyéni tartománynevet a tárfiókkal való használatra. További részletek: [Configure a custom domain name for your blob storage endpoint](storage-custom-domain-name.md) (Egyéni tartományév konfigurálása a Blob Storage-végponthoz).

### <a name="service-co-location-with-an-affinity-group"></a>Szolgáltatások közös elhelyezése affinitáscsoporttal
Az *affinitáscsoport* az Azure szolgáltatásainak és virtuális gépeinek földrajzi csoportosítása Azure tárfiókjával. Az affinitáscsoport javíthatja a szolgáltatás teljesítményét, ha a számítási feladatok azonos adatközpontba vagy a célközönség közelébe vannak helyezve. Emellett a kimenő adatforgalmi díjak sem merülnek fel, ha a tárfiókban lévő adatokat egy olyan másik szolgáltatás használja, amely ugyanabban az affinitáscsoportban van.

> [!NOTE]
> Affinitáscsoport létrehozásához nyissa meg a <b>Beállítások</b> területet a [klasszikus Azure portálon](https://manage.windowsazure.com), kattintson az <b>Affinitáscsoportok</b> elemre, majd az <b>Affinitáscsoport hozzáadása</b> lehetőségre vagy a <b>Hozzáadás</b> gombra. Affinitáscsoportokat az Azure szolgáltatásfelügyeleti API használatával is létrehozhat és kezelhet. További információk: <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">Operations on affinity groups</a> (Műveletek affinitáscsoportokkal).
> 
> 

## <a name="view-copy-and-regenerate-storage-access-keys"></a>Tárelérési kulcsok megtekintése, másolása és újragenerálása
Tárfiók létrehozásakor az Azure létrehoz két 512 bites tárelérési kulcsot, amelyek a hitelesítéshez használhatóak a tárfiók elérésekor. Azáltal, hogy az Azure két tárelérési kulcsot biztosít, lehetővé teszi a kulcsok újragenerálását anélkül, hogy megszakadna a társzolgáltatás vagy az ahhoz való hozzáférés.

> [!NOTE]
> Javasoljuk, hogy a tárelérési kulcsokat ne ossza meg senkivel. Ha a tárolási erőforrásokhoz az elérési kulcsok kiadása nélkül szeretné engedélyezni a hozzáférést, használhat *közös hozzáférésű jogosultságkódot*. A közös hozzáférésű jogosultságkód hozzáférést biztosít a fiókban lévő egyes erőforrásokhoz az Ön által meghatározott időtartamra és engedélyekkel. További információk: [Using Shared Access Signatures (SAS) (Közös hozzáférésű jogosultságkód (SAS) használata)](storage-dotnet-shared-access-signature-part-1.md)
> 
> 

A [klasszikus Azure portálon](https://manage.windowsazure.com) az irányítópult **Kulcsok kezelése** részében vagy a **Tárolás** lapon tekintheti meg, másolhatja és generálhatja újra a Blob, a Table és a Queue szolgáltatások eléréséhez használt tárelérési kulcsokat.

### <a name="copy-a-storage-access-key"></a>Tárelérési kulcs másolása
A **Kulcsok kezelése** részben másolhatja a tárelérési kulcsokat a kapcsolati karakterláncokban való használathoz. A kapcsolati karakterlánchoz a tárfiók neve és egy, a hitelesítés során használt kulcs szükséges. A kapcsolati karakterláncok az Azure Storage szolgáltatások elérésére való konfigurálásával kapcsolatos információk: [Configure Azure Storage Connection Strings](storage-configure-connection-string.md) (Azure Storage kapcsolati karakterláncok konfigurálása).

1. A [klasszikus Azure portálon](https://manage.windowsazure.com) kattintson a **Tárolás** elemre, majd a tárfiók nevére az irányítópult megnyitásához.
2. Kattintson a **Kulcsok kezelése** elemre.
   
     Megnyílik az **Elérési kulcsok kezelése** felület.
   
    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)
3. A tárelérési kulcs másolásához válassza ki a kulcs szövegét. Ezután kattintson jobb gombbal, majd kattintson a **Másolás** lehetőségre.

### <a name="regenerate-storage-access-keys"></a>Tárelérési kulcsok újragenerálása
Javasoljuk, hogy rendszeres időközönként cserélje le tárfiókja elérési kulcsait tárolási kapcsolatai biztonságának megőrzése érdekében. A rendszer két tárelérési kulcsot biztosít, így az egyik kulcs újragenerálása esetén a másikkal közben fenntarthatja a tárfiók kapcsolatait.

> [!WARNING]
> A tárelérési kulcsok újragenerálása hatással lehet a szolgáltatásokra az Azure-ban, valamint a tárfióktól függő saját alkalmazásaira is. A tárfiók eléréséhez a tárelérési kulcsot használó összes ügyfelet frissíteni kell, hogy az új kulcsot használják.
> 
> 

**Médiaszolgáltatások** – Ha rendelkezik a tárfióktól függő médiaszolgáltatásokkal, újra kell szinkronizálnia a tárelérési kulcsokat a médiaszolgáltatással, miután újragenerálta azokat.

**Alkalmazások** – Ha a tárfiókot használó webalkalmazásokkal vagy felhőszolgáltatásokkal rendelkezik, a kulcsok újragenerálásával elveszti a kapcsolatokat, kivéve, ha rotálja a kulcsokat. 

**Tártallózók** – Ha bármilyen [tártallózó alkalmazást](storage-explorers.md) használ, valószínűleg frissítenie kell az alkalmazások által használt tárkulcsot.

A tárelérési kulcsok az alábbi folyamat végrehajtásával rotálhatóak:

1. Frissítse a kapcsolati karakterláncokat az alkalmazás kódjában, hogy a tárfiók másodlagos elérési kulcsára hivatkozzanak.
2. Generálja újra a tárfiók elsődleges elérési kulcsát. A [klasszikus Azure portál](https://manage.windowsazure.com) irányítópultján vagy **Konfigurálás** lapján kattintson a **Kulcsok kezelése** elemre. Kattintson az **Újragenerálás** gombra az elsődleges elérési kulcs alatt, majd kattintson az **Igen** gombra annak megerősítéséhez, hogy egy új kulcsot kíván létrehozni.
3. Frissítse a kapcsolati karakterláncokat a kódban, hogy az új elsődleges tárelérési kulcsra hivatkozzanak.
4. Generálja újra a másodlagos elérési kulcsot.

## <a name="delete-a-storage-account"></a>Tárfiók törlése
A már nem használt tárfiókok törléséhez használja a **Törlés** gombot az irányítópulton vagy a **Konfigurálás** lapon. A **Törlés** funkció törli a teljes tárfiókot, beleértve a fiókban található összes blobot, táblát és üzenetsort.

> [!WARNING]
> A törölt tárfiókokat nem lehet visszaállítani, és nem lehet a törlés előtt abban tárolt tartalmakat helyreállítani. A fiók törlése előtt készítsen biztonsági másolatot minden menteni kívánt tartalomról. Ez igaz a fiókban lévő összes erőforrásra is – ha töröl egy blobot, táblát, üzenetsort vagy fájlt, az véglegesen törölve lesz.
> 
> Ha a tárfiók VHD-fájlokat tartalmaz egy Azure virtuális géphez, törölnie kell az ezeket a VHD-fájlokat használó összes lemezképet és lemezt a tárfiók törlése előtt. Először állítsa le a virtuális gépet, ha fut, majd törölje. Lemezek törléséhez lépjen a **Lemezek** lapra, és töröljön minden ott található lemezt. Lemezképek törléséhez lépjen a **Lemezképek** lapra, és törölje az összes, a fiókban tárolt lemezképet.
> 
> 

1. A [klasszikus Azure portálon](https://manage.windowsazure.com), kattintson a **Tárolás** elemre.
2. Kattintson bárhová a tárfiókbejegyzésben annak nevét kivéve, majd kattintson a **Törlés** lehetőségre.
   
     – Vagy –
   
    Kattintson a tárfiók nevére az irányítópult megnyitásához, majd kattintson a **Törlés** gombra.
3. Kattintson az **Igen** gombra annak megerősítéséhez, hogy törölni kívánja a tárfiókot.

## <a name="next-steps"></a>Következő lépések
* Az Azure Storage szolgáltatással kapcsolatos további információkért lásd [az Azure Storage-dokumentációt](https://azure.microsoft.com/documentation/services/storage/).
* Látogasson el [az Azure Storage csapat blogjára](http://blogs.msdn.com/b/windowsazurestorage/).
* [Adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md)




<!--HONumber=Dec16_HO1-->


