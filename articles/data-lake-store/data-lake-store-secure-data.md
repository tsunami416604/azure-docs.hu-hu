---
title: Az Azure Data Lake Storage Gen1 szolgáltatásban tárolt adatok biztonságossá tétele | Microsoft dokumentumok
description: Megtudhatja, hogyan biztosíthatja az adatokat az Azure Data Lake Storage Gen1 csoportokkal és hozzáférés-vezérlési listákkal
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: cebdff5ed233516683df3330e8fd3332ded664e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260305"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1-ben tárolt adatok védelme
Az Azure Data Lake Storage Gen1 szolgáltatásban az adatok biztonságossá tétele három lépéses megközelítés.  Mind a szerepköralapú hozzáférés-vezérlést (RBAC), mind a hozzáférés-vezérlési listákat (ACL) úgy kell beállítani, hogy teljes mértékben lehetővé tegyék az adatokhoz való hozzáférést a felhasználók és a biztonsági csoportok számára.

1. Először hozzon létre biztonsági csoportokat az Azure Active Directoryban (AAD). Ezek a biztonsági csoportok szerepköralapú hozzáférés-vezérlés (RBAC) megvalósításához használhatók az Azure Portalon. További információt a [Szerepköralapú hozzáférés-vezérlés a Microsoft Azure-ban](../role-based-access-control/role-assignments-portal.md)című témakörben talál.
2. Rendelje hozzá az AAD biztonsági csoportokat a Data Lake Storage Gen1 fiókhoz. Ez szabályozza a hozzáférést a Data Lake Storage Gen1 fiókhoz a portálról és a felügyeleti műveleteka portálról vagy API-kból.
3. Rendelje hozzá az AAD biztonsági csoportokat hozzáférés-vezérlési listaként (ACL)-ként a Data Lake Storage Gen1 fájlrendszeren.
4. Emellett beállíthat egy IP-címtartományt is a Data Lake Storage Gen1-ben lévő adatokhoz hozzáférő ügyfelek számára.

Ez a cikk útmutatást nyújt az Azure Portal használatával a fenti feladatok végrehajtásához. ARról, hogy a Data Lake Storage Gen1 hogyan valósítja meg a fiók- és adatszintű biztonságot, az [Azure Data Lake Storage Gen1 biztonsága című témakörben talál részletes](data-lake-store-security-overview.md)információt. Az ACL-ek Data Lake Storage Gen1 szolgáltatásban történő megvalósításának [részletes információiból a Hozzáférés-vezérlés áttekintése a Data Lake Storage Gen1 szolgáltatásban](data-lake-store-access-control.md)című témakörben olvashat.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen1 fiók**. Az azure [Data Lake Storage gen1](data-lake-store-get-started-portal.md) című témakörben talál útmutatást a létrehozásról.

## <a name="create-security-groups-in-azure-active-directory"></a>Biztonsági csoportok létrehozása az Azure Active Directoryban
Az AAD biztonsági csoportok létrehozásáról és a felhasználók csoporthoz való hozzáadásáról az [Azure Active Directory biztonsági csoportjainak kezelése](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)című témakörben talál.

> [!NOTE] 
> Az Azure Portal használatával felhasználókat és más csoportokat is hozzáadhat egy csoporthoz az Azure AD-ben. Azonban annak érdekében, hogy egy egyszerű szolgáltatás hozzáadása egy csoporthoz, használja [az Azure AD PowerShell-modul.](../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md)
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
Amikor felhasználókat vagy biztonsági csoportokat rendel a Data Lake Storage Gen1 fiókokhoz, az Azure Portal és az Azure Resource Manager API-k használatával szabályozza a fiók felügyeleti műveleteihez való hozzáférést. 

1. Nyisson meg egy Data Lake Storage Gen1 fiókot. A bal oldali ablaktáblában kattintson a **Minden erőforrás**elemre, majd a Minden erőforrás panelen kattintson arra a fióknévre, amelyhez felhasználót vagy biztonsági csoportot szeretne rendelni.

2. A Data Lake Storage Gen1 fiókpanelen kattintson **a Hozzáférés-vezérlés (IAM)** elemre. A panel alapértelmezés szerint az előfizetés-tulajdonosokat tekinti tulajdonosnak.
   
    ![Biztonsági csoport hozzárendelése az Azure Data Lake Storage Gen1 fiókhoz](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Biztonsági csoport hozzárendelése az Azure Data Lake Storage Gen1 fiókhoz")

3. A **Hozzáférés-vezérlés (IAM)** panelen kattintson a **Hozzáadás** gombra az **Engedélyek hozzáadása** panel megnyitásához. Az **Engedélyek hozzáadása** panelen jelöljön ki egy **szerepkört** a felhasználóhoz/csoporthoz. Keresse meg az Azure Active Directoryban korábban létrehozott biztonsági csoportot, és jelölje ki. Ha sok felhasználóés csoport közül szeretne keresni, a **Kijelölés** szövegmezővel szűrhet a csoport nevére. 
   
    ![Szerepkör hozzáadása a felhasználószámára](./media/data-lake-store-secure-data/adl.add.user.1.png "Szerepkör hozzáadása a felhasználószámára")
   
    A **tulajdonos** és **a közreműködői** szerepkör hozzáférést biztosít a data lake-fiók különböző felügyeleti funkcióihoz. Azon felhasználók számára, akik a data lake-ben lévő adatokkal kommunikálnak, de továbbra is meg kell tekinteniük a fiókkezelési adatokat, hozzáadhatja őket az **Olvasó** szerepkörhöz. Ezek a szerepkörök hatóköre a Data Lake Storage Gen1 fiókhoz kapcsolódó felügyeleti műveletekre korlátozódik.
   
    Az adatműveletek esetében az egyes fájlrendszer-engedélyek határozzák meg, hogy a felhasználók mit tehetnek. Ezért az Olvasó szerepkörrel rendelkező felhasználók csak a fiókhoz társított felügyeleti beállításokat tekinthetik meg, de potenciálisan olvashatják és írhatják az adatokat a hozzájuk rendelt fájlrendszer-engedélyek alapján. A Data Lake Storage Gen1 fájlrendszer engedélyeit [a Biztonsági csoport hozzárendelése a CL-ek hozzárendelése az Azure Data Lake Storage Gen1 fájlrendszerhez](#filepermissions)írja le.

    > [!IMPORTANT]
    > Csak a **Tulajdonos** szerepkör engedélyezi automatikusan a fájlrendszer elérését. A **közreműködő**, **a Reader**és az összes többi szerepkör höz hozzáférés-hozzáférés szükséges a mappákhoz és fájlokhoz való bármilyen szintű hozzáférés engedélyezéséhez.  The **Owner** role provides super-user file and folder permissions that cannot be overridden via ACLs. Ha többet szeretne tudni arról, hogy az RBAC-házirendek hogyan felelnek meg az adateléréshez, olvassa el [az RBAC fiókkezelést](data-lake-store-security-overview.md#rbac-for-account-management)című témakörben.

4. Ha olyan csoportot/felhasználót szeretne hozzáadni, amely nem szerepel az **Engedélyek hozzáadása** panelen, meghívhatja őket, ha beírja az e-mail címüket a **Szöveg kijelölése** mezőbe, majd kiválasztja őket a listából.
   
    ![Biztonsági csoport hozzáadása](./media/data-lake-store-secure-data/adl.add.user.2.png "Biztonsági csoport hozzáadása")
   
5. Kattintson a **Mentés** gombra. Az alábbiakban látható módon kell látnia a hozzáadott biztonsági csoportot.
   
    ![Biztonsági csoport hozzáadva](./media/data-lake-store-secure-data/adl.add.user.3.png "Biztonsági csoport hozzáadva")

6. A felhasználó/biztonsági csoport most már hozzáfér a Data Lake Storage Gen1 fiókhoz. Ha hozzáférést szeretne biztosítani bizonyos felhasználóknak, hozzáadhatja őket a biztonsági csoporthoz. Hasonlóképpen, ha vissza szeretné vonni egy felhasználó hozzáférését, eltávolíthatja őket a biztonsági csoportból. Egy fiókhoz több biztonsági csoportot is hozzárendelhet. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Felhasználók vagy biztonsági csoportok hozzárendelése AC-ként a Data Lake Storage Gen1 fájlrendszerhez
Ha felhasználói/biztonsági csoportokat rendel a Data Lake Storage Gen1 fájlrendszerhez, beállíthatja a hozzáférés-vezérlést a Data Lake Storage Gen1-ben tárolt adatokon.

1. A Data Lake Storage Gen1 fiókpanelen kattintson az **Adatkezelő**elemre.
   
    ![Adatok megtekintése az Adatkezelőben](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Adatok megtekintése az Adatkezelőben")
2. Az **Adatkezelő** panelen kattintson arra a mappára, amelyhez konfigurálni szeretné az ACL-t, majd kattintson az **Access**gombra. Ha a hozzáférés-kezelőket fájlhoz szeretné rendelni, először a fájlra kell kattintania az előnézet megtekintéséhez, majd a **Fájlelőnézet** panelen kattintson az **Access** gombra.
   
    ![ACL-ek beállítása a Data Lake Storage Gen1 fájlrendszeren](./media/data-lake-store-secure-data/adl.acl.1.png "ACL-ek beállítása a Data Lake Storage Gen1 fájlrendszeren")
3. Az **Access** panel felsorolja a legfelső szintű tulajdonosokat és a hozzárendelt engedélyeket. Click the **Add** icon to add additional Access ACLs.
    > [!IMPORTANT]
    > Az egyetlen fájl hozzáférési engedélyeinek beállítása nem feltétlenül ad hozzáférést a felhasználónak/csoportnak a fájlhoz. A fájl elérési útjának elérhetőnek kell lennie a hozzárendelt felhasználó/csoport számára. További információt és példákat az [engedélyekkel kapcsolatos gyakori forgatókönyvek](data-lake-store-access-control.md#common-scenarios-related-to-permissions)című témakörben talál.
   
    ![Szabványos és egyéni hozzáférés listázása](./media/data-lake-store-secure-data/adl.acl.2.png "Szabványos és egyéni hozzáférés listázása")
   
   * A **Tulajdonosok** és **mindenki más** unix-stílusú hozzáférést biztosít, ahol írási, írási, végrehajtási (rwx) három különböző felhasználói osztályhoz adható meg: tulajdonos, csoport és mások.
   * **A hozzárendelt engedélyek** megfelelnek a POSIX Hozzáférés-engedélyeknek, amelyek lehetővé teszik, hogy a fájl tulajdonosán vagy csoportján kívül meghatározott elnevezett felhasználókvagy csoportok számára engedélyeket állítson be. 
     
     További információ: [HDFS ACLs](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Az ACL-ek Adattótároló gen1-ben történő megvalósításáról a [Hozzáférés-vezérlés a Data Lake Storage Gen1 alkalmazásban](data-lake-store-access-control.md)című témakörben talál további információt.
4. Kattintson a **Hozzáadás** ikonra az **Engedélyek hozzárendelése** panel megnyitásához. Ebben a panelen kattintson **a Felhasználó vagy csoport kiválasztása**elemre, majd a Felhasználó vagy csoport **kiválasztása** panelen keresse meg az Azure Active Directoryban korábban létrehozott biztonsági csoportot. Ha sok csoportból szeretne keresni, a felső szövegmező segítségével szűrje a csoport nevét. Kattintson a hozzáadni kívánt csoportra, majd a **Kijelölés gombra.**
   
    ![Csoport hozzáadása](./media/data-lake-store-secure-data/adl.acl.3.png "Csoport hozzáadása")
5. Kattintson az **Engedélyek kiválasztása**gombra, válassza ki az engedélyeket, hogy az engedélyeket rekurzívmódon kell-e alkalmazni, és hogy az engedélyeket hozzáférési engedélyhez, alapértelmezett Hozzáférés-kulcszó-kulcshoz vagy mindkettőhöz kívánja-e rendelni. Kattintson az **OK** gombra.
   
    ![Engedélyek hozzárendelése a csoporthoz](./media/data-lake-store-secure-data/adl.acl.4.png "Engedélyek hozzárendelése a csoporthoz")
   
    A Data Lake Storage Gen1 és az Default/Access ACL-ok engedélyeiről a [Hozzáférés-vezérlés a Data Lake Storage Gen1 alkalmazásban](data-lake-store-access-control.md)című témakörben olvashat bővebben.
6. Miután az **Engedélyek kiválasztása** panelen az **Ok** gombra kattintott, az újonnan hozzáadott csoport és a kapcsolódó engedélyek megjelennek az **Access** panelen.
   
    ![Engedélyek hozzárendelése a csoporthoz](./media/data-lake-store-secure-data/adl.acl.5.png "Engedélyek hozzárendelése a csoporthoz")
   
   > [!IMPORTANT]
   > Az aktuális kiadásban legfeljebb 28 bejegyzés lehet a **Hozzárendelt engedélyek**csoportban. Ha 28-nál több felhasználót szeretne hozzáadni, hozzon létre biztonsági csoportokat, adjon hozzá felhasználókat a biztonsági csoportokhoz, adjon hozzá hozzáférést ezekhez a biztonsági csoportokhoz a Data Lake Storage Gen1 fiókhoz.
   > 
   > 
7. Szükség esetén a csoport hozzáadása után is módosíthatja a hozzáférési engedélyeket. Törölje vagy jelölje be az egyes engedélytípusok (Olvasás, Írás, Végrehajtás) jelölőnégyzetét annak alapján, hogy el kívánja-e távolítani vagy hozzá kívánja rendelni az engedélyt a biztonsági csoporthoz. A módosítások mentéséhez kattintson a **Mentés** gombra, a módosítások visszavonásához pedig az **Elvetés gombra.**

## <a name="set-ip-address-range-for-data-access"></a>AZ adatok eléréséhez az IP-címtartomány beállítása
A Data Lake Storage Gen1 lehetővé teszi az adattárhoz való hálózati hozzáférés hálózati szintű további zárolását. Engedélyezheti a tűzfalat, megadhat egy IP-címet, vagy ip-címtartományt adhat meg a megbízható ügyfelek számára. Ha engedélyezve van, csak azok az ügyfelek csatlakozhatnak az üzlethez, amelyek a megadott tartományon belül rendelkeznek az IP-címekkel.

![Tűzfal-beállítások és IP-hozzáférés](./media/data-lake-store-secure-data/firewall-ip-access.png "Tűzfal beállításai és IP-címe")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 fiók biztonsági csoportjainak eltávolítása
Amikor eltávolítja a biztonsági csoportokat a Data Lake Storage Gen1 fiókokból, csak az Azure Portal és az Azure Resource Manager API-k használatával módosítja a fiók felügyeleti műveleteihez való hozzáférést.  

Access to data is unchanged and is still managed by the access ACLs.  Ez alól kivételt képeznek a Tulajdonosok szerepkörfelhasználói/csoportjai.  A Tulajdonosok szerepkörből eltávolított felhasználók/csoportok már nem szuperfelhasználók, és hozzáférésük visszaesik az ACL-beállítások eléréséhez. 

1. A Data Lake Storage Gen1 fiókpanelen kattintson **a Hozzáférés-vezérlés (IAM)** elemre. 
   
    ![Biztonsági csoport hozzárendelése a Data Lake Storage Gen1 fiókhoz](./media/data-lake-store-secure-data/adl.select.user.icon.png "Biztonsági csoport hozzárendelése a Data Lake Storage Gen1 fiókhoz")
2. A **hozzáférés-vezérlés (IAM)** panelen kattintson az eltávolítani kívánt biztonsági csoport(ok)ra. Kattintson az **Eltávolítás** lehetőségre.
   
    ![Biztonsági csoport eltávolítva](./media/data-lake-store-secure-data/adl.remove.group.png "Biztonsági csoport eltávolítva")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Biztonsági csoport ACL-jainak eltávolítása a Data Lake Storage Gen1 fájlrendszerből
Ha eltávolítja a biztonsági csoport ACL-ok egy Data Lake Storage Gen1 fájlrendszer, módosítja az adatokhoz való hozzáférést a Data Lake Storage Gen1 fiókban.

1. A Data Lake Storage Gen1 fiókpanelen kattintson az **Adatkezelő**elemre.
   
    ![Könyvtárak létrehozása a Data Lake Storage Gen1 fiókban](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Könyvtárak létrehozása a Data Lake Storage Gen1 fiókban")
2. Az **Adatkezelő** panelen kattintson arra a mappára, amelynek hozzáférés-cl-jét el szeretné távolítani, majd kattintson az **Access gombra.** Ha el szeretné távolítani egy fájl Hozzáférés-visszafért, először a fájlra kell kattintania az előnézet megtekintéséhez, majd kattintson az **Access** elemre a **Fájlelőnézet** panelen. 
   
    ![ACL-ek beállítása a Data Lake Storage Gen1 fájlrendszeren](./media/data-lake-store-secure-data/adl.acl.1.png "ACL-ek beállítása a Data Lake Storage Gen1 fájlrendszeren")
3. Az **Access** panelen kattintson az eltávolítani kívánt biztonsági csoportra. Az **Access részletei** panelen kattintson az **Eltávolítás**gombra.
   
    ![Engedélyek hozzárendelése a csoporthoz](./media/data-lake-store-secure-data/adl.remove.acl.png "Engedélyek hozzárendelése a csoporthoz")

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake storage gen1 áttekintése](data-lake-store-overview.md)
* [Adatok másolása az Azure Storage Blobs szolgáltatásból a Data Lake Storage Gen1 szolgáltatásba](data-lake-store-copy-data-azure-storage-blob.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Az 1. generációs Data Lake Storage használatának első lépései a PowerShell-lel](data-lake-store-get-started-powershell.md)
* [A Data Lake Storage Gen1 használatának első lépései a .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [Hozzáférés a Data Lake Storage Gen1 diagnosztikai naplóihoz](data-lake-store-diagnostic-logs.md)

