---
title: Azure Data Lake Store-ban tárolt adatok védelme |} Microsoft Docs
description: Útmutató az Azure Data Lake Store csoportok használatával adatvédelem és hozzáférés-vezérlési listák
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 5d91d8d00a37422dcead195354447d602b801f9e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Azure Data Lake Store-ban tárolt adatok védelme
Adatok védelme az Azure Data Lake Store egy három lépéses megközelítés.  Mindkét szerepköralapú hozzáférés-vezérlés (RBAC), és hozzáférés-vezérlési listák (ACL) kell beállítani, hogy használhassa a felhasználók és biztonsági csoportok adatokhoz való hozzáférés.

1. Először hozzon létre biztonsági csoportokat az Azure Active Directory (AAD). Ezek a biztonsági csoportok segítségével valósítja meg a szerepköralapú hozzáférés-vezérlést (RBAC) Azure-portálon. További információkért lásd: [szerepköralapú hozzáférés-vezérlés a Microsoft Azure-ban](../role-based-access-control/role-assignments-portal.md).
2. Az AAD-biztonságicsoportok hozzárendelése az Azure Data Lake Store-fiók. Ez szabályozza a hozzáférést a portál vagy API-k a portál és felügyeleti műveletek a Data Lake Store-fiókhoz.
3. Rendelje hozzá az AAD-biztonságicsoportok hozzáférés vezérlési listák (ACL) a Data Lake Store fájlrendszerben.
4. Az ügyfelek, amelyek férhet hozzá az adatokhoz, a Data Lake Store továbbá az IP-címtartományok is beállíthatja.

Ez a cikk útmutatást a fenti feladatok végezhetők el az Azure-portál használatával. Hogyan valósítja meg a Data Lake Store a biztonsági fiók és az adatok szinten részletes információkért lásd: [az Azure Data Lake Store biztonsági](data-lake-store-security-overview.md). A hozzáférés-vezérlési listákat az Azure Data Lake Store megvalósított hogyan részletesen információkért lásd: [áttekintése a hozzáférés-vezérlés a Data Lake Store](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Store-fiók**. Hogyan hozhat létre ilyet, lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Az Azure Active Directory biztonsági csoportok létrehozása
Az AAD biztonsági csoportok létrehozása és felhasználók hozzáadása a csoporthoz, lásd: [az Azure Active Directory biztonsági csoportok kezelése](../active-directory/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Adhat hozzá felhasználókat és az egyéb csoportok is egy csoportot az Azure ad-ben az Azure portál használatával. Azonban ahhoz, hogy egy egyszerű szolgáltatásnév hozzáadása egy csoporthoz, használjon [az Azure AD PowerShell modul](../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-azure-data-lake-store-accounts"></a>Felhasználók vagy biztonsági csoportok hozzárendelése az Azure Data Lake Store-fiók
Amikor a felhasználók vagy biztonsági csoportok hozzárendelése az Azure Data Lake Store-fiókok, a fiók az Azure portál és az Azure Resource Manager API-k használata a kezelési műveletek elérését Ön szabályozza. 

1. Egy Azure Data Lake Store-fiók megnyitása. A bal oldali ablaktáblán kattintson **összes erőforrás**, majd kattintson a fiók nevét, amelyhez a felhasználót vagy biztonsági csoportot hozzárendelni kívánt összes erőforrások paneljén.

2. A Data Lake Store-fiók panelen kattintson a **hozzáférés-vezérlés (IAM)**. A panel alapértelmezés szerint az előfizetésnél tulajdonos tulajdonosaként sorolja fel.
   
    ![Biztonsági csoport hozzárendelése az Azure Data Lake Store-fiók](./media/data-lake-store-secure-data/adl.select.user.icon.png "hozzárendelése biztonsági csoport az Azure Data Lake Store-fiók")

3. Az a **hozzáférés-vezérlés (IAM)** panelen kattintson a **Hozzáadás** megnyitásához a **engedélyek hozzáadása** panel. Az a **engedélyek hozzáadása** panelen válassza a **szerepkör** a felhasználó vagy csoport. Keresse meg a korábban létrehozott Azure Active Directory biztonsági csoport, és válassza ki azt. Ha a felhasználók és csoportok keresése a nagy, használja a **válasszon** szövegdobozra kattintva a csoport nevére. 
   
    ![A felhasználó szerepkör hozzáadása](./media/data-lake-store-secure-data/adl.add.user.1.png "a felhasználó szerepkör hozzáadása")
   
    A **tulajdonos** és **közreműködő** szerepkör számos felügyeleti feladatot hozzáférést biztosítanak a data lake-fiók. A felhasználók számára, akik a data lake, de továbbra is felügyeleti fiókadatok megtekintéséhez szükséges adatokkal együtt fog működni, akkor is hozzáadhatja őket a a **olvasó** szerepkör. Ezek a szerepkörök hatóköre az Azure Data Lake Store-fiók kapcsolódó felügyeleti műveletek korlátozva.
   
    Műveletek egyes fájlrendszerre vonatkozó engedélyek megadása a végezhető műveletek. Ezért egy olvasó szerepkört rendelkező felhasználói is csak a fiókhoz tartozó felügyeleti beállítások megtekintése, de is potenciálisan olvasási és írási adatokat a hozzájuk rendelt fájlrendszerbeli engedélyeket alapján. Data Lake Store fájlrendszerre vonatkozó engedélyek ismerteti a következő [hozzárendelése biztonsági csoportra, mint a hozzáférés-vezérlési listák és az Azure Data Lake Store-fájlrendszer](#filepermissions).

    > [!IMPORTANT]
    > Csak a **tulajdonos** szerepkör automatikusan lehetővé teszi, hogy a fájlrendszer elérése. A **közreműködő**, **olvasó**, és minden egyéb szerepkörökhöz szükség hozzáférés-vezérlési listák ahhoz, hogy a fájlok és mappák hozzáférési szinttel.  A **tulajdonos** szerepkör biztosít felettes felhasználói fájlok és mappák engedélyeit, hogy az ACL-ek keresztül nem lesz felülbírálható. Az RBAC-házirendek hogyan adatelérési hozzárendelését további információkért lásd: [felhasználóifiók-kezelés az RBAC](data-lake-store-security-overview.md#rbac-for-account-management).

4. Ha egy csoport vagy felhasználó nem szerepel a hozzáadni kívánt a **engedélyek hozzáadása** panelen felajánlhatja őket írja be az e-mail címüket a **kiválasztása** szövegmezőbe, és jelölje be őket a listából.
   
    ![Biztonsági csoport hozzáadása](./media/data-lake-store-secure-data/adl.add.user.2.png "biztonsági csoport hozzáadása")
   
5. Kattintson a **Save** (Mentés) gombra. Meg kell jelennie a biztonsági csoportot hozzáadni a lent látható módon.
   
    ![Biztonsági csoport hozzáadott](./media/data-lake-store-secure-data/adl.add.user.3.png "hozzáadott biztonsági csoport")

6. A felhasználók vagy biztonsági csoport hozzáférhet az Azure Data Lake Store-fiók. Ha azt szeretné, hogy hozzáférést biztosítson az adott felhasználóknak, később is hozzáadhatja a biztonsági csoporthoz. Hasonlóképpen ha szeretné visszavonni a hozzáférést egy felhasználó számára, eltávolíthatja azokat a biztonsági csoportból. Több biztonsági csoportot is rendelhet egy fiókot. 

## <a name="filepermissions"></a>Felhasználók vagy biztonsági csoportok hozzárendelése az Azure Data Lake Store-fájlrendszer hozzáférés-vezérlési listákat
Felhasználók vagy biztonsági csoportok hozzárendelése az Azure Data Lake fájlrendszer, állítsa a hozzáférés-vezérlés az Azure Data Lake Store-ban tárolt adatokat.

1. A Data Lake Store-fiók panelén kattintson a **Data Explorer** (Adatkezelő) elemre.
   
    ![Adatok Explorerben adatainak megtekintéséhez](./media/data-lake-store-secure-data/adl.start.data.explorer.png "keresztül adatkezelő adatok megtekintése")
2. Az a **adatkezelő** panelen kattintson arra a mappára, konfigurálja a hozzáférés-vezérlési lista, és kattintson a kívánt **hozzáférés**. ACL-ek hozzárendelése a fájl, akkor először válassza a fájlt, tekintse meg a lapot, majd **hozzáférés** a a **fájljának előnézete** panelen.
   
    ![Hozzáférés-vezérlési listák beállítása a Data Lake fájlrendszerben](./media/data-lake-store-secure-data/adl.acl.1.png "beállítása ACL-ek Data Lake fájlrendszer")
3. A **hozzáférés** panel tulajdonosai sorolja fel, és már hozzá van rendelve a legfelső szintű engedélyekkel. Kattintson a **Hozzáadás** ikonra kattintva adja hozzá a további hozzáférés-vezérlési listák.
    > [!IMPORTANT]
    > Egy fájl hozzáférési engedélyeinek beállítása nem feltétlenül hozzáférést egy felhasználó vagy csoport, amely a fájl. A fájl elérési útját a hozzárendelt felhasználói csoport számára elérhetőnek kell lennie. További információk és példák: [engedélyekkel kapcsolatos gyakori helyzetek](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Normál és egyéni hozzáférési listában](./media/data-lake-store-secure-data/adl.acl.2.png "normál és egyéni hozzáférési listában")
   
   * A **tulajdonosok** és **mindenki más** UNIX típusú hozzáférést biztosítson, amelyben meg kell határoznia Olvasás, írás, három különböző felhasználói osztályok (rwx) végre: tulajdonos, csoport és mások számára.
   * **Engedélyeket az** megfelel-e a POSIX hozzáférés-vezérlési listákat, amelyek lehetővé teszik, hogy az engedélyek beállítása az adott nevesített felhasználók vagy csoportok túl a fájl tulajdonosa, vagy a csoport. 
     
     További információkért lásd: [HDFS hozzáférés-vezérlési listák](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). A hozzáférés-vezérlési listákat a Data Lake Store megvalósított hogyan további információkért lásd: [hozzáférés-vezérlés a Data Lake Store](data-lake-store-access-control.md).
4. Kattintson a **Hozzáadás** ikonra kattintva nyissa meg a **engedélyeket** panelen. Ezen a panelen kattintson a **válassza ki a felhasználó vagy csoport**, majd a **válassza ki a felhasználó vagy csoport** panelen keresse meg a korábban létrehozott Azure Active Directory biztonsági csoport. Ha a keresés csoportok számos, a szövegmezőbe írja felső szűréséhez használja a csoport nevére. Kattintson a csoport hozzáadása, és kattintson a kívánt **válasszon**.
   
    ![Csoport hozzáadása](./media/data-lake-store-secure-data/adl.acl.3.png "csoport hozzáadása")
5. Kattintson a **engedélyként válassza**, jelölje be a engedélyek, hogy az engedélyeket alkalmazni kell a rekurzív módon, és hogy szeretné-e az engedélyek hozzárendelése egy hozzáférés ACL, alapértelmezett hozzáférés-vezérlési lista, vagy mindkettőt. Kattintson az **OK** gombra.
   
    ![Engedélyeket csoportosításához](./media/data-lake-store-secure-data/adl.acl.4.png "csoportosításához engedélyek hozzárendelése")
   
    A Data Lake Store és a hozzáférés-vezérlési listákat alapértelmezett/hozzáférési engedélyekkel kapcsolatos további információkért lásd: [hozzáférés-vezérlés a Data Lake Store](data-lake-store-access-control.md).
6. Kattintás után **Ok** a a **engedélyként válassza** panel újonnan létrehozott csoport és az engedélyek most megjelenik a **hozzáférés** panelen.
   
    ![Engedélyeket csoportosításához](./media/data-lake-store-secure-data/adl.acl.5.png "csoportosításához engedélyek hozzárendelése")
   
   > [!IMPORTANT]
   > A jelenlegi kiadásban, bejegyzések legfeljebb 28 lehet **engedélyeket az**. Ha azt szeretné, egynél 28 felhasználók hozzáadása, készítsen biztonsági csoportok, felhasználók biztonsági csoportok hozzáadása, adja hozzá ezeket a csoportokat hozzáférést adhat a Data Lake Store-fiók.
   > 
   > 
7. Ha szükséges, módosíthatja a hozzáférési engedélyeket a csoporthoz való hozzáadását követően. Törölje vagy jelölje be az egyes engedély (olvasási, írási, végrehajtási) alapján, hogy távolítsa el vagy az, hogy engedélyeket rendeljenek hozzá a biztonsági csoport. Kattintson a **mentése** menti a módosításokat, vagy **elvetése** visszavonja a módosításokat.

## <a name="set-ip-address-range-for-data-access"></a>Állítsa be az IP-címtartomány a eléréséhez
Azure Data Lake Store lehetővé teszi a hálózati szintű adattár eléréséhez további zárolása. Tűzfal engedélyezése, IP-cím vagy IP-címtartomány megadása a megbízható ügyfeleket. Miután engedélyezte őket, csak a definiált tartományon belüli IP-címmel rendelkező ügyfelek az áruházból is elérheti.

![Tűzfalbeállítások és IP-hozzáférési](./media/data-lake-store-secure-data/firewall-ip-access.png "tűzfalbeállítások és IP-cím")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Távolítsa el az Azure Data Lake Store-fiók biztonsági csoportok
Biztonsági csoportok az Azure Data Lake Store-fiókból való eltávolításakor csak hozzáférést módosítja a fiók az Azure portál és az Azure Resource Manager API-k használata a felügyeleti műveletekhez.  

Adatokhoz való hozzáférés nem változott, és továbbra is kezeli az ACL-ek hozzáférés.  A kivételt a rendszer felhasználókat/csoportokat a tulajdonosok szerepkörben.  Felhasználók/csoportok eltávolítja a tulajdonosok szerepkör már nincs felügyelők és a hozzáférésüket visszaáll hozzáférési hozzáférés-szabályozási beállítások. 

1. A Data Lake Store-fiók panelen kattintson a **hozzáférés-vezérlés (IAM)**. 
   
    ![Biztonsági csoport hozzárendelése az Azure Data Lake-fiók](./media/data-lake-store-secure-data/adl.select.user.icon.png "hozzárendelése biztonsági csoport az Azure Data Lake-fiók")
2. Az a **hozzáférés-vezérlés (IAM)** panelen kattintson a biztonsági csoportot el szeretné távolítani. Kattintson a **eltávolítása**.
   
    ![Biztonsági csoport eltávolítása](./media/data-lake-store-secure-data/adl.remove.group.png "biztonsági csoport eltávolítása")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Azure Data Lake Store-fájlrendszer hozzáférés-vezérlési listák biztonsági csoport eltávolítása
Ha eltávolítja az ACL-ek biztonsági csoport az Azure Data Lake Store-fájlrendszer, módosítja hozzáférés az adatok a Data Lake Store-ban.

1. A Data Lake Store-fiók panelén kattintson a **Data Explorer** (Adatkezelő) elemre.
   
    ![Könyvtárak létrehozása a Data Lake-fiókban](./media/data-lake-store-secure-data/adl.start.data.explorer.png "könyvtárak létrehozása a Data Lake-fiókban")
2. Az a **adatkezelő** panelen kattintson arra a mappára, távolítsa el a hozzáférés-vezérlési lista, és kattintson a kívánt **hozzáférés**. Egy fájl hozzáférés-vezérlési listák eltávolításához először kattintson a fájlt, tekintse meg a lapot, majd **hozzáférés** a a **fájljának előnézete** panelen. 
   
    ![Hozzáférés-vezérlési listák beállítása a Data Lake fájlrendszerben](./media/data-lake-store-secure-data/adl.acl.1.png "beállítása ACL-ek Data Lake fájlrendszer")
3. Az a **hozzáférés** panelen kattintson a biztonsági csoportot el szeretné távolítani. Az a **részleteit** panelen kattintson a **eltávolítása**.
   
    ![Engedélyeket csoportosításához](./media/data-lake-store-secure-data/adl.remove.acl.png "csoportosításához engedélyek hozzárendelése")

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [Adatok másolása az Azure Storage Blobs Data Lake Store-bA](data-lake-store-copy-data-azure-storage-blob.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Ismerkedés a Data Lake Store PowerShell használatával](data-lake-store-get-started-powershell.md)
* [Ismerkedés a Data Lake Store .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [A Data Lake Store diagnosztikai naplóinak elérése](data-lake-store-diagnostic-logs.md)

