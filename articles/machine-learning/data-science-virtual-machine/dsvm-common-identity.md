---
title: Közös identitás beállítása az adatok tudományos virtuálisgép - Azure |} Microsoft Docs
description: A telepítő közös identitás vállalati team DSVM környezetekben.
keywords: a mélyhivatkozással tanulási, AI adatok tudományos eszközök, a adatok tudományos virtuális gép, a földrajzi analytics, a csapat az tudományos folyamata
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 70c6b8cd147cefaa3128bc1e6a414a6fa61dba6d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830894"
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>Közös identitás beállítása az adatok tudományos virtuális gépen

Alapértelmezés szerint az Azure virtuális gépen, beleértve az adatok tudományos virtuális gép (DSVM) helyi felhasználói fiókokat a virtuális gép kiépítése során jönnek létre, és a felhasználók hitelesítéséhez a virtuális géphez a ezeket a hitelesítő adatokat. Ha több virtuális géphez való hozzáférést igénylő, ez a megközelítés gyorsan megjeleníthet nehézkes lehet, hogy hitelesítő adatok kezelése. Általános felhasználói fiókok és felügyeleti történő szabványalapú identitásszolgáltató lehetővé teszi a hitelesítő adatok egyetlen halmazába több erőforrást, beleértve a több DSVMs Azure elérésére használhat. 

Active Directory (AD) egy népszerű identitásszolgáltató és támogatott mind az Azure platformon egy szolgáltatás, valamint a helyszíni. Kihasználhatja a AD vagy az Azure AD hitelesíti a felhasználókat a tudományos virtuális gép (DSVM) önálló vagy a fürtben, a DSVM egy Azure virtuálisgép-méretezési készlet. Ehhez a DSVM példányok csatlakoztatása egy Active Directory-tartománynak. Ha már rendelkezik egy Active Directory az identitáskezelést, használhatja a közös identitás-szolgáltatóként. Abban az esetben, ha még nem rendelkezik az AD, a felügyelt AD az Azure segítségével futtathatja egy nevű szolgáltatás [Azure Active Directory tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/)(az Azure AD DS). 

A dokumentációja [Azure Active directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) biztosít részletes [utasításokat](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) kezelt active Directoryban, beleértve az Azure AD csatlakozni a helyszíni címtár, ha nem rendelkezik ilyennel. 

Ez a cikk többi állítson be egy teljes körűen felügyelt AD tartományi szolgáltatások, Azure-ban az Azure Active Directory tartományi szolgáltatások és a DSVMs csatlakoztatása a felügyelt AD-tartomány ahhoz, hogy felhasználók hozzáférhessenek az egyes közös felhasználói fiók és a hitelesítő adatok használatával DSVMs (és más Azure-erőforrások) lépéseit ismerteti. 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Állítson be egy teljes körűen felügyelt, az Azure Active Directory-tartomány

Az Azure Active Directory tartományi szolgáltatások leegyszerűsíti az identitások kezeléséhez Azure teljes körűen felügyelt szolgáltatás megadásával. Active directory-tartomány, a felhasználók és csoportok kezelése.  A lépések beállítása az Azure AD tartományi üzemeltetett, és a címtárban lévő felhasználói fiók:

1. Felhasználó hozzáadása az Active directory portálon 

    a. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
    
    b. Válassza ki **Azure Active Directory** , majd **felhasználók és csoportok**.
    
    c. A **felhasználók és csoportok**, jelölje be **minden felhasználó**, majd válassza ki **új felhasználó**.
   
   ![Az Add parancs kiválasztása](./media/add-user.png)
    
    d. Adja meg a felhasználó, például a **neve** és **felhasználónév**. A tartomány a felhasználónév részét kell lennie, a kezdeti alapértelmezett tartomány neve "[neve].onmicrosoft.com" vagy egy ellenőrzött és érvényes, nem összevont [egyéni tartománynév](../../active-directory/add-custom-domain.md) például "contoso.com".
    
    e. Másolja, vagy ellenkező esetben vegye figyelembe a létrehozott felhasználói jelszót, így megadhatja azt a felhasználó számára a folyamat befejezése után.
    
    f. Másik lehetőségként megnyithatja és töltse ki **profil**, **csoportok**, vagy **Directory szerepkör** a felhasználó számára. 
    
    g. A **felhasználói**, jelölje be **létrehozása**.
    
    h. Az új felhasználó számára létrehozott jelszót biztonságos terjesztése, hogy a felhasználói bejelentkezés.

2.  Az Azure AD tartományi szolgáltatások létrehozására

    Hozzon létre egy Azure ad hozzá, kövesse a cikkben lévő utasítások "[engedélyezése Azure Active Directory tartományi szolgáltatások az Azure portál használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)" (feladat 1 – 5. feladat). Fontos, hogy a meglévő felhasználói jelszavakat az Active directory frissülnek, hogy az Azure Active Directory tartományi szolgáltatásokban a jelszó szinkronizálva van. Fontos továbbá hozzáadása az Azure Active Directory tartományi Szolgáltatásokban, lásd a fenti cikk #4. feladat: a DNS-ben. 

3.  Hozzon létre egy külön DSVM alhálózatot # 2. feladat az portáladatbázis előző lépésben létrehozott virtuális hálózatban
4.  Hozzon létre egy vagy több adat tudományos Virtuálisgép-példányok DSVM alhálózaton 
5.  Hajtsa végre a [utasításokat](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) DSVM ad hozzáadni. 
6.  Ezután csatlakoztassa egy megosztott Azure fájlok tárolásához, a home vagy notebook directory ahhoz, hogy a munkaterület csatlakoztatása bármelyik olyan gépen. (Ha szoros fájl szintű engedélyek szüksége lesz egy vagy több virtuális gépeken futó NFS)

    a. [Az Azure-fájlmegosztás létrehozása](../../storage/files/storage-how-to-create-file-share.md)
    
    b. A Linux DSVM csatlakoztassa azt. Amikor kattint a "Csatlakozás" gombra az Azure-fájlok az Azure portálon a tárfiókban lévő, a parancs futhat a Linux DSVM rendszerhéjakba jelenik meg. A parancs a következőképpen jelenik meg:
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  Tegyük fel például, csatlakoztatta /data/workspace Azure-fájljait. Most hozzon létre könyvtárak minden a megosztáson található, a felhasználók. /Data/Workspace/user1, /data/workspace/user2 és így tovább. Hozzon létre egy ```notebooks``` könyvtárhoz, az egyes felhasználók munkaterületen. 
8. Hozzon létre szimbolikus hivatkozásokat a ```notebooks``` a ```$HOME/userx/notebooks/remote```.   

Meg kell, a felhasználók az active Directoryban tárolt az Azure-ban, és képes-e jelentkezni a DSVM (SSH, Jupyterhub), amely az Azure Active Directory tartományi szolgáltatások AD hitelesítő adataival csatlakozik. Mivel a felhasználó munkaterület Azure megosztott fájlokon, a felhasználó rendelkezik a jegyzetfüzetek és más feladatok bármely DSVM való hozzáférést, Jupyterhub használatakor. 

Az automatikus skálázás használhatja a virtuálisgép-méretezési készlet létrehozásához egy olyan virtuális gépek, amelyek összes csatlakoznak a tartományhoz, ilyen módon, és a megosztott lemez van beállítva. Felhasználók jelentkezzen be a virtuálisgép-méretezési csoportban lévő elérhető gépi, és hozzáférhetnek a notebookok tároló megosztott lemez. 

## <a name="next-steps"></a>További lépések

* [Biztonságosan tárolni a felhőben lévő erőforrások eléréséhez szükséges hitelesítő adatokat](dsvm-secure-access-keys.md)



