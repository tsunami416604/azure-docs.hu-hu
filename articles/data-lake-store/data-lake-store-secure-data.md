---
title: Az Azure Data Lake Storage Gen1 tárolt adatok védelme |} A Microsoft Docs
description: Ismerje meg, hogyan használata az adatok megóvására az Azure Data Lake Storage Gen1 csoportok és hozzáférés-vezérlési listák
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 15ef1e1b96a0da24714b4ddbda6b24f1f6f33ae0
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46296736"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 tárolt adatok védelme
Az adatok védelme az Azure Data Lake Storage Gen1 egy három lépéses megközelítést.  Mindkét szerepköralapú hozzáférés-vezérlés (RBAC), és a hozzáférés-vezérlési listák (ACL) kell állítani a felhasználók és biztonsági csoportok adatokhoz való hozzáférés teljes mértékű engedélyezéséhez.

1. Először hozzon létre biztonsági csoportokat az Azure Active Directory (AAD). A biztonsági csoportok használatban vannak a szerepköralapú hozzáférés-vezérlés (RBAC) megvalósítása az Azure Portalon. További információkért lásd: [szerepköralapú hozzáférés-vezérlés a Microsoft Azure-ban](../role-based-access-control/role-assignments-portal.md).
2. Data Lake Storage Gen1 fiókjához hozzárendelni az AAD biztonsági csoportokat. Ez vezérli a hozzáférést a portál vagy API-k a portál és a felügyeleti műveletek a Data Lake Storage Gen1 fiókhoz.
3. Hozzáférés-vezérlési listák (ACL) a Data Lake Storage Gen1 fájlrendszerben az AAD biztonsági csoportokat rendeljen hozzá.
4. Data Lake Storage Gen1 lévő adatokat elérő ügyfelek emellett egy IP-címtartományt is beállíthat.

Ez a cikk útmutatást nyújt a fenti feladatok elvégzéséhez az Azure portal használatával. Hogyan valósítja meg a Data Lake Storage Gen1 biztonsági a fiók és adatok szintjén a részletes információkért lásd: [biztonság az Azure Data Lake Storage Gen1](data-lake-store-security-overview.md). Hogyan találhatók meg hozzáférés-vezérlési listák Data Lake Storage Gen1 részletes információkért lásd: [áttekintése a hozzáférés-vezérlés a Data Lake Storage Gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen1 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Az Azure Active Directory biztonsági csoportok létrehozása
AAD biztonsági csoportok létrehozásával és felhasználók hozzáadása a csoporthoz, lásd: [kezelése az Azure Active Directory biztonsági csoportok](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Adhat hozzá felhasználókat és a többi csoport egy csoportot az Azure ad-ben az Azure portal használatával. Azonban ahhoz, hogy a szolgáltatásnév hozzáadása a csoporthoz, használható [az Azure AD PowerShell-modul](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Felhasználók vagy biztonsági csoportok hozzárendelése a Data Lake Storage Gen1 fiókokhoz
Amikor a felhasználók vagy biztonsági csoportok hozzárendelése Data Lake Storage Gen1 fiókokhoz, a műveletek az Azure Portallal és az Azure Resource Manager API-k a fiók elérését Ön szabályozza. 

1. Nyissa meg a Data Lake Storage Gen1 fiókot. A bal oldali ablaktáblában kattintson **összes erőforrás**, és az összes erőforrás panelen kattintson a fiók nevét, amelyhez szeretné egy felhasználó vagy biztonsági csoport hozzárendelése.

2. A Data Lake Storage Gen1 fiók panelén kattintson **hozzáférés-vezérlés (IAM)**. A panel alapértelmezés szerint az előfizetés-tulajdonost tulajdonosként sorolja fel.
   
    ![Biztonsági csoport hozzárendelése az Azure Data Lake Storage Gen1 fiók](./media/data-lake-store-secure-data/adl.select.user.icon1.png "biztonsági csoport hozzárendelése az Azure Data Lake Storage Gen1 fiókhoz")

3. Az a **hozzáférés-vezérlés (IAM)** panelen kattintson a **Hozzáadás** megnyitásához a **engedélyek hozzáadása** panelen. Az a **engedélyek hozzáadása** panelen válassza ki a **szerepkör** a felhasználó vagy csoport. Keresse meg a korábban létrehozott Azure Active Directory biztonsági csoport, és válassza ki azt. Ha a felhasználók és csoportok kell keresni a sok, használja a **kiválasztása** a csoport nevére szűrésére szolgáló szövegmező. 
   
    ![A felhasználó szerepkör hozzáadása](./media/data-lake-store-secure-data/adl.add.user.1.png "a felhasználó szerepkör hozzáadása")
   
    A **tulajdonosa** és **közreműködői** szerepkör számos felügyeleti feladatot a hozzáférést biztosítsanak a data lake-fiók. A felhasználók számára, akik működjön együtt a data lake, ugyanakkor a fiók felügyeleti információk megtekintéséhez szükséges adatok, hozzáadhatja őket a **olvasó** szerepkör. Ezek a szerepkörök hatókörének a felügyeleti műveletek a Data Lake Storage Gen1 fiókkal kapcsolatos korlátozódik.
   
    Adatműveletek egyes fájlrendszerre vonatkozó engedélyekkel határozza meg a felhasználók mit tehetnek. Ezért egy olvasói szerepkörrel rendelkező felhasználói is csak a fiókhoz tartozó felügyeleti beállítások megtekintése, de képes potenciálisan olvasási és írási hozzájuk rendelt fájlrendszeri engedélyek alapján. Data Lake Storage Gen1 fájlrendszerre vonatkozó engedélyekkel címen leírt [biztonsági csoport hozzárendelése ACL-ként az Azure Data Lake Storage Gen1 fájlrendszerhez](#filepermissions).

    > [!IMPORTANT]
    > Csak a **tulajdonosa** szerepkör automatikusan lehetővé teszi, hogy a fájlrendszer elérése. A **közreműködői**, **olvasó**, és a többi szerepkör szükséges ahhoz, hogy bármilyen szinten fájlok és mappák hozzáférési ACL-ek.  A **tulajdonosa** szerepkör felügyelő fájl- és mappaengedélyeket, amely nem bírálható felül az ACL-alapú biztosít. Hogyan leképezése az RBAC-házirendeket adatelérési további információkért lásd: [fiókkezeléshez RBAC](data-lake-store-security-overview.md#rbac-for-account-management).

4. Ha szeretné-e, amely nem szerepel a csoport vagy felhasználó hozzáadása a **engedélyek hozzáadása** panelen meghívhatja munkatársait, azokat a saját e-mail cím beírásával a **kiválasztása** szövegmezőbe, és válassza a listából.
   
    ![Adja meg a biztonsági csoport](./media/data-lake-store-secure-data/adl.add.user.2.png "biztonsági csoport hozzáadása")
   
5. Kattintson a **Save** (Mentés) gombra. A biztonsági csoport hozzáadva a lent látható módon kell megjelennie.
   
    ![Biztonsági csoport hozzáadva](./media/data-lake-store-secure-data/adl.add.user.3.png "biztonsági csoport hozzáadása")

6. A felhasználó vagy biztonsági csoport mostantól a Data Lake Storage Gen1 fiók hozzáféréssel rendelkezik. Ha szeretne hozzáférést biztosítanak az egyes felhasználókhoz, felveheti őket a biztonsági csoporthoz. Hasonlóképpen ha vissza szeretné vonni a hozzáférést egy felhasználó, eltávolíthatja őket a biztonsági csoportból. Több biztonsági csoportot is rendelhet egy fiókot. 

## <a name="filepermissions"></a>Felhasználók vagy biztonsági csoport hozzárendelése ACL-ként a Data Lake Storage Gen1 fájlrendszer
A Data Lake Storage Gen1 fájlrendszer rendelhet a felhasználó vagy biztonsági csoporttal, beállíthat egy hozzáférés-vezérlés a Data Lake Storage Gen1 tárolt adatokat.

1. A Data Lake Storage Gen1 fiók panelén kattintson **adatkezelő**.
   
    ![Megtekintheti az adatkezelő segítségével adatokat](./media/data-lake-store-secure-data/adl.start.data.explorer.png "adatkezelő keresztül adatok megtekintése")
2. Az a **adatkezelő** panelen kattintson arra a mappára, amelyhez konfigurálni az ACL-t, és kattintson a kívánt **hozzáférés**. ACL-ek hozzárendelése egy fájlt, először kattintson a tekintse meg a lapot, majd kattintson a fájl **hozzáférés** származó a **Fájlelőnézet** panelen.
   
    ![Állítsa be a hozzáférés-vezérlési listák Data Lake Storage Gen1 fájlrendszerben](./media/data-lake-store-secure-data/adl.acl.1.png "beállítva hozzáférés-vezérlési listák Data Lake Storage Gen1 fájlrendszer")
3. A **hozzáférés** panel felsorolja a tulajdonosok között, és már hozzá van rendelve a legfelső szintű engedélyekkel. Kattintson a **Hozzáadás** ikonra kattintva adja hozzá a további hozzáférési ACL-ek.
    > [!IMPORTANT]
    > Egyetlen fájl a hozzáférési engedélyek beállítása nem feltétlenül biztosít hozzáférést egy felhasználó/csoport, amely a fájl. A fájl elérési útját a hozzárendelt felhasználói csoportok számára elérhetőnek kell lennie. További információért és példákért lásd: [az engedélyekhez kapcsolódó gyakori helyzetek](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Standard és egyéni hozzáférési listán](./media/data-lake-store-secure-data/adl.acl.2.png "szabványos és egyéni hozzáférési listázása")
   
   * A **tulajdonosok** és **mindenki más** UNIX típusú hozzáférést biztosíthat, adhatja meg Olvasás, írás, hajtsa végre (rwx) a három különböző felhasználói osztályok: tulajdonos, csoport és mások.
   * **Hozzárendelt engedélyek** a POSIX hozzáférés-vezérlési listák, amelyek lehetővé teszik, hogy az engedélyek beállítása az adott nevesített felhasználók vagy csoportok a fájl tulajdonosa vagy a csoport nem felel meg. 
     
     További információkért lásd: [HDFS hozzáférés-vezérlési listák](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Hogyan találhatók meg hozzáférés-vezérlési listák Data Lake Storage Gen1 további információkért lásd: [hozzáférés-vezérlés a Data Lake Storage Gen1](data-lake-store-access-control.md).
4. Kattintson a **Hozzáadás** ikonra kattintva nyissa meg a **engedélyek hozzárendelése** panelen. Ezen a panelen kattintson a **felhasználó vagy csoport kiválasztása**, majd **felhasználó vagy csoport kiválasztása** panelen keresse meg a korábban létrehozott Azure Active Directory biztonsági csoport. Ha csoportokat kell keresni a sok, a szövegmező felső szűréséhez használja a csoport nevére. Kattintson a csoport hozzáadása, és kattintson a kívánt **kiválasztása**.
   
    ![Csoport hozzáadása](./media/data-lake-store-secure-data/adl.acl.3.png "csoport hozzáadása")
5. Kattintson a **engedélyek kiválasztása**, jelölje be az engedélyeket, hogy engedélyeket kell alkalmazni rekurzív módon és, hogy kívánja-e az engedélyek hozzárendelése a hozzáférési ACL-t, mint alapértelmezett ACL-t, vagy mindkettőt. Kattintson az **OK** gombra.
   
    ![Engedélyek csoporthoz való hozzárendelése](./media/data-lake-store-secure-data/adl.acl.4.png "csoporthoz engedélyek hozzárendelése")
   
    A Data Lake Storage Gen1 és alapértelmezett/hozzáférési ACL-ek engedélyekkel kapcsolatos további információkért lásd: [hozzáférés-vezérlés a Data Lake Storage Gen1](data-lake-store-access-control.md).
6. Kattintás után **Ok** a a **engedélyek kiválasztása** panelen, az újonnan létrehozott csoport és az engedélyek most már megjelenik a **hozzáférés** panelen.
   
    ![Engedélyek csoporthoz való hozzárendelése](./media/data-lake-store-secure-data/adl.acl.5.png "csoporthoz engedélyek hozzárendelése")
   
   > [!IMPORTANT]
   > A jelenlegi kiadásban, használhat akár 28 bejegyzések **engedélyeket az**. Ha azt szeretné, több mint 28 felhasználók hozzáadása, hozzunk létre biztonsági csoportokat, felhasználók hozzáadása a biztonsági csoportok, adja hozzá ezeket a csoportokat a Data Lake Storage Gen1 fiók elérést biztosíthat.
   > 
   > 
7. Szükség esetén módosíthatja is a hozzáférési engedélyeket a csoport hozzáadása után. Törölje a jelet, vagy jelölje be az egyes engedélyt (olvasási, írási, végrehajtási) alapján, hogy távolítsa el, vagy hogy engedélyeket rendeljenek hozzá a biztonsági csoport. Kattintson a **mentése** menti a módosításokat, vagy **elveti** visszavonja a módosításokat.

## <a name="set-ip-address-range-for-data-access"></a>Állítsa be az IP-címtartományt az adatok eléréséhez
Data Lake Storage Gen1 lehetővé teszi további zárolási hálózati szinten az adattárhoz való hozzáférés. Tűzfal engedélyezése, adja meg az IP-cím vagy IP-címtartomány megadása a megbízható ügyfelek számára. Ha engedélyezve van, csak a definiált tartományon belüli IP-címek rendelkező ügyfelek a tároló csatlakozhat.

![Tűzfalbeállítások és IP-hozzáférési](./media/data-lake-store-secure-data/firewall-ip-access.png "tűzfalbeállítások és IP-cím")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Biztonsági csoportok egy Data Lake Storage Gen1 fiók eltávolítása
Amikor a biztonsági csoportok eltávolítja a Data Lake Storage Gen1 fiókok, csak változtatja a műveletek az Azure Portallal és az Azure Resource Manager API-k a fiók hozzáférési.  

Adatokhoz való hozzáférés nem változik, és továbbra is felügyeli a hozzáférési ACL-ek.  A kivétel ez alól a tulajdonosok szerepkörű felhasználók/csoportok.  Felhasználók/csoportok eltávolítva a tulajdonos szerepkör már nem felügyelők és hozzáférésüket visszavált hozzáférési ACL-beállítások. 

1. A Data Lake Storage Gen1 fiók panelén kattintson **hozzáférés-vezérlés (IAM)**. 
   
    ![Biztonsági csoport hozzárendelése a Data Lake Storage Gen1 fiók](./media/data-lake-store-secure-data/adl.select.user.icon.png "biztonsági csoport hozzárendelése a Data Lake Storage Gen1 fiókhoz")
2. Az a **hozzáférés-vezérlés (IAM)** panelen kattintson az eltávolítani kívánt biztonsági csoport(ok). Kattintson a **eltávolítása**.
   
    ![Biztonsági csoport eltávolítva](./media/data-lake-store-secure-data/adl.remove.group.png "biztonsági csoport eltávolítva")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>A Data Lake Storage Gen1 fájlrendszer ACL-ek biztonsági csoport eltávolítása
Ha eltávolítja az ACL-ek biztonsági csoport egy Data Lake Storage Gen1 fájlrendszer, a Data Lake Storage Gen1 fiókban lévő adatok hozzáférés módosítja.

1. A Data Lake Storage Gen1 fiók panelén kattintson **adatkezelő**.
   
    ![Könyvtárak létrehozása a Data Lake Storage Gen1 fiókban](./media/data-lake-store-secure-data/adl.start.data.explorer.png "könyvtárak létrehozása a Data Lake Storage Gen1-fiókban")
2. Az a **adatkezelő** panelen kattintson arra a mappára, amelyhez az ACL eltávolítása, és kattintson a kívánt **hozzáférés**. Egy fájl hozzáférés-vezérlési listák eltávolításához először kattintson a tekintse meg a lapot, majd kattintson a fájl **hozzáférés** a a **Fájlelőnézet** panelen. 
   
    ![Állítsa be a hozzáférés-vezérlési listák Data Lake Storage Gen1 fájlrendszerben](./media/data-lake-store-secure-data/adl.acl.1.png "beállítva hozzáférés-vezérlési listák Data Lake Storage Gen1 fájlrendszer")
3. Az a **hozzáférés** panelen kattintson az eltávolítani kívánt biztonsági csoportot. Az a **részleteit** panelen kattintson a **eltávolítása**.
   
    ![Engedélyek csoporthoz való hozzárendelése](./media/data-lake-store-secure-data/adl.remove.acl.png "csoporthoz engedélyek hozzárendelése")

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Get Started with Data Lake Storage Gen1 PowerShell-lel](data-lake-store-get-started-powershell.md)
* [Get Started with Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Data Lake Storage Gen1 diagnosztikai naplóinak elérése](data-lake-store-diagnostic-logs.md)

