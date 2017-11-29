---
title: "Azure Data Lake Store-ban tárolt adatok védelme |} Microsoft Docs"
description: "Útmutató az Azure Data Lake Store csoportok használatával adatvédelem és hozzáférés-vezérlési listák"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e246abaf8d1d7939765e1c878f6dfaf6375294f6
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="securing-data-stored-in-azure-data-lake-store"></a>Azure Data Lake Store-ban tárolt adatok védelme
Adatok védelme az Azure Data Lake Store egy három lépéses megközelítés.

1. Először hozzon létre biztonsági csoportokat az Azure Active Directory (AAD). Ezek a biztonsági csoportok segítségével valósítja meg a szerepköralapú hozzáférés-vezérlést (RBAC) Azure-portálon. További információkért lásd: [szerepköralapú hozzáférés-vezérlés a Microsoft Azure-ban](../active-directory/role-based-access-control-configure.md).
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

1. Egy Azure Data Lake Store-fiók megnyitása. A bal oldali ablaktáblán kattintson **Tallózás**, kattintson a **Data Lake Store**, majd a Data Lake Store panelen kattintson a fiók nevét, amelyhez Ön szeretne rendelni egy felhasználót vagy biztonsági csoportot.

2. A Data Lake Store-fiók beállítások panelen kattintson a **hozzáférés-vezérlés (IAM)**. A panel alapértelmezett listák által **előfizetés rendszergazdái** tulajdonos csoportot.
   
    ![Biztonsági csoport hozzárendelése az Azure Data Lake Store-fiók](./media/data-lake-store-secure-data/adl.select.user.icon.png "hozzárendelése biztonsági csoport az Azure Data Lake Store-fiók")

    Két módon vegye fel azt a csoportot, és megfelelő szerepköröket.
   
    * Felhasználó/csoport hozzáadása a fiókot, és hozzárendelheti a szerepkör, vagy
    * Adja hozzá a szerepkört, és hozzárendelheti a felhasználók/csoportok szerepkörhöz.
     
    Ebben a szakaszban úgy tekintünk, az első módszer, majd a szerepkörök hozzárendelése és a csoport hozzáadása. Először válassza ki a szerepkört, majd rendelje hozzá az csoportokat ehhez a szerepkörhöz hasonló lépéseket végezheti el.
4. Az a **felhasználók** panelen kattintson a **Hozzáadás** megnyitásához a **hozzáférés hozzáadása** panelen. Az a **hozzáférés hozzáadása** panelen kattintson a **Szerepkörválasztás**, és válassza ki a szerepkört a felhasználó vagy csoport.
   
    ![A felhasználó szerepkör hozzáadása](./media/data-lake-store-secure-data/adl.add.user.1.png "a felhasználó szerepkör hozzáadása")
   
    A **tulajdonos** és **közreműködő** szerepkör számos felügyeleti feladatot hozzáférést biztosítanak a data lake-fiók. A felhasználók számára, akik a data Lake-adatokkal együtt fog működni, akkor is hozzáadhatja őket a a ** olvasó ** szerepkör. Ezek a szerepkörök hatóköre az Azure Data Lake Store-fiók kapcsolódó felügyeleti műveletek korlátozva.
   
    Az adatok műveletek egyedi fájlrendszerre vonatkozó engedélyek megadása a végezhető műveletek. Ezért egy olvasó szerepkört rendelkező felhasználói is csak a fiókhoz tartozó felügyeleti beállítások megtekintése, de is potenciálisan olvasási és írási adatokat a hozzájuk rendelt fájlrendszerbeli engedélyeket alapján. Data Lake Store fájlrendszerre vonatkozó engedélyek ismerteti a következő [hozzárendelése biztonsági csoportra, mint a hozzáférés-vezérlési listák és az Azure Data Lake Store-fájlrendszer](#filepermissions).
5. Az a **hozzáférés hozzáadása** panelen kattintson a **felhasználók hozzáadása az** megnyitásához a **felhasználók hozzáadása** panelen. Ezen a panelen keresse meg a korábban létrehozott Azure Active Directory biztonsági csoport. Ha a keresés csoportok számos, a szövegmezőbe írja felső szűréséhez használja a csoport nevére. Kattintson a **Kiválasztás** gombra.
   
    ![Biztonsági csoport hozzáadása](./media/data-lake-store-secure-data/adl.add.user.2.png "biztonsági csoport hozzáadása")
   
    Ha egy csoport vagy felhasználó nem szerepel a hozzáadandó felajánlhatja őket használatával a **meghívása** ikonra, és a felhasználó vagy csoport e-mail címének megadása.
6. Kattintson az **OK** gombra. Meg kell jelennie a biztonsági csoportot hozzáadni a lent látható módon.
   
    ![Biztonsági csoport hozzáadott](./media/data-lake-store-secure-data/adl.add.user.3.png "hozzáadott biztonsági csoport")

7. A felhasználók vagy biztonsági csoport hozzáférhet az Azure Data Lake Store-fiók. Ha azt szeretné, hogy hozzáférést biztosítson az adott felhasználóknak, később is hozzáadhatja a biztonsági csoporthoz. Hasonlóképpen ha szeretné visszavonni a hozzáférést egy felhasználó számára, eltávolíthatja azokat a biztonsági csoportból. Több biztonsági csoportot is rendelhet egy fiókot. 

## <a name="filepermissions"></a>Felhasználók vagy biztonsági csoport hozzárendelése az Azure Data Lake Store-fájlrendszer hozzáférés-vezérlési listákat
Felhasználók vagy biztonsági csoportok hozzárendelése az Azure Data Lake fájlrendszer, állítsa a hozzáférés-vezérlés az Azure Data Lake Store-ban tárolt adatokat.

1. A Data Lake Store-fiók panelén kattintson a **Data Explorer** (Adatkezelő) elemre.
   
    ![Könyvtárak létrehozása a Data Lake Store-fiók](./media/data-lake-store-secure-data/adl.start.data.explorer.png "könyvtárak létrehozása a Data Lake-fiókban")
2. Az a **adatkezelő** panelen kattintson a fájl vagy mappa legyen a hozzáférés-vezérlési lista konfigurálása, és kattintson **hozzáférés**. Egy fájl hozzáférés-vezérlési lista rendeléséhez kattintson **hozzáférés** a a **fájljának előnézete** panelen.
   
    ![Hozzáférés-vezérlési listák beállítása a Data Lake fájlrendszerben](./media/data-lake-store-secure-data/adl.acl.1.png "beállítása ACL-ek Data Lake fájlrendszer")
3. A **hozzáférés** panel szabványos hozzáférési és egyéni hozzáférési már hozzá van rendelve a legfelső szintű sorolja fel. Kattintson a **Hozzáadás** ikonra kattintva adja hozzá az egyéni szintű hozzáférés-vezérlési listák.
   
    ![Normál és egyéni hozzáférési listában](./media/data-lake-store-secure-data/adl.acl.2.png "normál és egyéni hozzáférési listában")
   
   * **Szabványos hozzáférési** van a UNIX-stílusú hozzáférést, amelyben meg kell határoznia Olvasás, írás, három különböző felhasználói osztályok (rwx) végre: tulajdonos, csoport és mások számára.
   * **Egyéni hozzáférési** megfelel-e a POSIX hozzáférés-vezérlési listákat, amely lehetővé teszi, hogy az engedélyek beállítása a megadott nevesített felhasználók vagy csoportok, és nem csak a fájl tulajdonosa vagy csoport. 
     
     További információkért lásd: [HDFS hozzáférés-vezérlési listák](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). A hozzáférés-vezérlési listákat a Data Lake Store megvalósított hogyan további információkért lásd: [hozzáférés-vezérlés a Data Lake Store](data-lake-store-access-control.md).
4. Kattintson a **Hozzáadás** ikonra kattintva nyissa meg a **egyéni hozzáférés hozzáadása** panelen. Ezen a panelen kattintson a **felhasználó vagy csoport kiválasztása**, majd a **felhasználó vagy csoport kiválasztása** panelen keresse meg a korábban létrehozott Azure Active Directory biztonsági csoport. Ha a keresés csoportok számos, a szövegmezőbe írja felső szűréséhez használja a csoport nevére. Kattintson a csoport hozzáadása, és kattintson a kívánt **válasszon**.
   
    ![Csoport hozzáadása](./media/data-lake-store-secure-data/adl.acl.3.png "csoport hozzáadása")
5. Kattintson a **Select engedélyeket**, engedélyek kiválasztása, és szeretne hozzárendelni az engedélyeket ACL alapértelmezés szerint, hogy elérhetők a hozzáférés-vezérlési lista, vagy mindkettőt. Kattintson az **OK** gombra.
   
    ![Engedélyeket csoportosításához](./media/data-lake-store-secure-data/adl.acl.4.png "csoportosításához engedélyek hozzárendelése")
   
    A Data Lake Store és a hozzáférés-vezérlési listákat alapértelmezett/hozzáférési engedélyekkel kapcsolatos további információkért lásd: [hozzáférés-vezérlés a Data Lake Store](data-lake-store-access-control.md).
6. Az a **egyéni hozzáférés hozzáadása** panelen kattintson a **OK**. Az újonnan létrehozott csoport a társított engedélyekkel most megjelenik a **hozzáférés** panelen.
   
    ![Engedélyeket csoportosításához](./media/data-lake-store-secure-data/adl.acl.5.png "csoportosításához engedélyek hozzárendelése")
   
   > [!IMPORTANT]
   > A jelenlegi kiadásban, csak akkor 9 bejegyzések **egyéni hozzáférési**. Ha azt szeretné, egynél 9-felhasználók hozzáadása, készítsen biztonsági csoportok felhasználók biztonsági csoportok hozzáadása, adja hozzá ezeket a csoportokat hozzáférést adhat a Data Lake Store-fiók.
   > 
   > 
7. Ha szükséges, módosíthatja a hozzáférési engedélyeket a csoporthoz való hozzáadását követően. Törölje vagy jelölje be az egyes engedély (olvasási, írási, végrehajtási) alapján, hogy távolítsa el vagy az, hogy engedélyeket rendeljenek hozzá a biztonsági csoport. Kattintson a **mentése** menti a módosításokat, vagy **elvetése** visszavonja a módosításokat.

## <a name="set-ip-address-range-for-data-access"></a>Állítsa be az IP-címtartomány a eléréséhez
Azure Data Lake Store lehetővé teszi a hálózati szintű adattár eléréséhez további zárolása. Tűzfal engedélyezése, IP-cím vagy IP-címtartomány megadása a megbízható ügyfeleket. Miután engedélyezte őket, csak a definiált tartományon belüli IP-címmel rendelkező ügyfelek az áruházból is elérheti.

![Tűzfalbeállítások és IP-hozzáférési](./media/data-lake-store-secure-data/firewall-ip-access.png "tűzfalbeállítások és IP-cím")

## <a name="remove-security-groups-for-an-azure-data-lake-store-account"></a>Távolítsa el az Azure Data Lake Store-fiók biztonsági csoportok
Biztonsági csoportok az Azure Data Lake Store-fiókból való eltávolításakor csak hozzáférést módosítja a fiók az Azure portál és az Azure Resource Manager API-k használata a felügyeleti műveletekhez.

1. A Data Lake Store-fiók panelen kattintson a **beállítások**. Az a **beállítások** panelen kattintson a **felhasználók**.
   
    ![Biztonsági csoport hozzárendelése az Azure Data Lake-fiók](./media/data-lake-store-secure-data/adl.select.user.icon.png "hozzárendelése biztonsági csoport az Azure Data Lake-fiók")
2. Az a **felhasználók** panelen kattintson a biztonsági csoportot el szeretné távolítani.
   
    ![Biztonsági csoport eltávolítása](./media/data-lake-store-secure-data/adl.add.user.3.png "biztonsági csoport eltávolítása")
3. A biztonsági csoport paneljén kattintson **eltávolítása**.
   
    ![Biztonsági csoport eltávolítása](./media/data-lake-store-secure-data/adl.remove.group.png "biztonsági csoport eltávolítása")

## <a name="remove-security-group-acls-from-azure-data-lake-store-file-system"></a>Azure Data Lake Store-fájlrendszer hozzáférés-vezérlési listák biztonsági csoport eltávolítása
Biztonsági csoportok hozzáférés-vezérlési listák eltávolítja az Azure Data Lake Store-fájlrendszer, amikor módosítja hozzáférést a Data Lake Store az adatokat.

1. A Data Lake Store-fiók panelén kattintson a **Data Explorer** (Adatkezelő) elemre.
   
    ![Könyvtárak létrehozása a Data Lake-fiókban](./media/data-lake-store-secure-data/adl.start.data.explorer.png "könyvtárak létrehozása a Data Lake-fiókban")
2. Az a **adatkezelő** panelen kattintson a fájlra vagy mappára, amelyen el szeretné távolítani a hozzáférés-vezérlési lista, majd a fiók panelen, kattintson a **hozzáférés** ikonra. Egy fájl hozzáférés-vezérlési lista eltávolításához kattintson **hozzáférés** a a **fájljának előnézete** panelen.
   
    ![Hozzáférés-vezérlési listák beállítása a Data Lake fájlrendszerben](./media/data-lake-store-secure-data/adl.acl.1.png "beállítása ACL-ek Data Lake fájlrendszer")
3. Az a **hozzáférés** panelen a a **egyéni hozzáférési** területen kattintson a biztonsági csoportot el szeretné távolítani. Az a **egyéni hozzáférési** panelen kattintson a **eltávolítása** , majd **OK**.
   
    ![Engedélyeket csoportosításához](./media/data-lake-store-secure-data/adl.remove.acl.png "csoportosításához engedélyek hozzárendelése")

## <a name="see-also"></a>Lásd még:
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [Adatok másolása az Azure Storage Blobs Data Lake Store-bA](data-lake-store-copy-data-azure-storage-blob.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Ismerkedés a Data Lake Store PowerShell használatával](data-lake-store-get-started-powershell.md)
* [Ismerkedés a Data Lake Store .NET SDK használatával](data-lake-store-get-started-net-sdk.md)
* [A Data Lake Store diagnosztikai naplóinak elérése](data-lake-store-diagnostic-logs.md)

