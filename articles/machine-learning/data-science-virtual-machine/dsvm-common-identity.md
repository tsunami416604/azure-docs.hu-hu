---
title: Az adatok tudományos virtuálisgép - Azure közös identitás beállítása |} Microsoft Docs
description: Állítson be egy közös identitás egy vállalati team DSVM környezetekben.
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
ms.openlocfilehash: d6235f3a425481a13e627d683bb4c3943b473b40
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309826"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Az adatok tudományos virtuális gép közös identitás beállítása

Egy Azure virtuális gép (VM), az adatok tudományos virtuális gép (DSVM), beleértve a helyi felhasználói fiókokat hozhat létre a virtuális gép kiépítése során. A felhasználók majd hitelesítik a virtuális gép ezen hitelesítő adatok használatával. Ha több virtuális géphez való hozzáférést igénylő, ezt a módszert is gyorsan karban lehessen nehézkes hitelesítő adatok kezelése közben. Általános felhasználói fiókok és egy szabványalapú identitásszolgáltató tartalomablakban engedélyezi, hogy a hitelesítő adatok egyetlen készletének használata az Azure, beleértve a több DSVMs több erőforrások eléréséhez. 

Az Active Directory egy népszerű identitásszolgáltató, és egy szolgáltatás és a helyszíni Azure támogatja. Akkor használhatja az Azure Active Directory (Azure AD) vagy a helyszíni Active Directory DSVM különálló, vagy egy Azure virtuálisgép-méretezési csoportban lévő DSVMs fürtben a felhasználók hitelesítésére. Ehhez a DSVM példányok csatlakoztatása egy Active Directory-tartományhoz. 

Ha már rendelkezik az Active Directory az identitáskezelést, használhatja a közös identitás-szolgáltatóként. Ha még nem rendelkezik az Active Directory, a felügyelt Active Directory-példányban Azure segítségével futtathatja nevű szolgáltatás [Azure Active Directory tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/) (az Azure AD DS). 

A dokumentációja [az Azure AD](https://docs.microsoft.com/azure/active-directory/) biztosít részletes [felügyeleti utasításokat](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity), beleértve az Azure AD csatlakozni a helyszíni címtár, ha nem rendelkezik ilyennel. 

Ez a cikk ismerteti a lépéseket egy teljes körűen felügyelt Azure Active Directory tartományi szolgáltatások beállítása az Azure Active Directory tartományi szolgáltatások használatával. A DSVMs majd csatlakoztatása a felügyelt Active Directory-tartomány ahhoz, hogy felhasználók hozzáférhessenek az DSVMs (és más Azure-erőforrások) készlete egy közös felhasználói fiók és a hitelesítő adatok használatával. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Állítson be egy teljes körűen felügyelt, az Azure Active Directory-tartomány

Az Azure Active Directory tartományi szolgáltatások leegyszerűsíti az identitások kezeléséhez Azure teljes körűen felügyelt szolgáltatás megadásával. Az Active Directory-tartomány kezelheti a felhasználókat és csoportokat. Állítsa be az Azure által kezelt Active Directory tartományt és felhasználói fiókot a címtárban lépései a következők:

1. Az Azure portálon vegye fel a felhasználót az Active Directory: 

   a. Jelentkezzen be a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
    
   b. Válassza ki **Azure Active Directory** , majd **felhasználók és csoportok**.
    
   c. A **felhasználók és csoportok**, jelölje be **minden felhasználó**, majd válassza ki **új felhasználó**.
   
      A **felhasználói** ablaktábla megnyitása.
      
      ![A "User" ablak](./media/add-user.png)
    
   d. Adja meg a felhasználó, például a **neve** és **felhasználónév**. A felhasználói tartomány részét kell lennie, vagy a kezdeti alapértelmezett tartomány neve "[neve].onmicrosoft.com", vagy egy ellenőrzött és érvényes, nem összevont [egyéni tartománynév](../../active-directory/add-custom-domain.md) például "contoso.com".
    
   e. Másolja, vagy ellenkező esetben vegye figyelembe a létrehozott felhasználói jelszót, így megadhatja azt a felhasználó számára a folyamat befejezése után.
    
   f. Másik lehetőségként megnyithatja és töltse ki **profil**, **csoportok**, vagy **Directory szerepkör** a felhasználó számára. 
    
   g. A **felhasználói**, jelölje be **létrehozása**.
    
   h. Az új felhasználó számára létrehozott jelszót biztonságos terjesztése, hogy a felhasználói bejelentkezés.

2. Az Azure Active Directory tartományi szolgáltatások példányt létrehozni. A cikk utasításait [engedélyezése Azure Active Directory tartományi szolgáltatások az Azure portál használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (feladatokat az 1-5). Fontos, a meglévő felhasználói jelszavakat az Active Directory frissítése, hogy az Azure Active Directory tartományi szolgáltatásokban a jelszó van-e szinkronizálva. Fontos továbbá DNS hozzáadása az Azure Active Directory tartományi szolgáltatások, a cikk 4. feladat leírtak szerint. 

3. Hozzon létre egy külön DSVM alhálózatot a 2. feladat az előző lépésben létrehozott virtuális hálózatban.
4. Hozzon létre egy vagy több adat tudományos Virtuálisgép-példányok DSVM alhálózaton. 
5. Kövesse a [utasításokat](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) DSVM hozzáadása az Active Directory. 
6. Csatlakoztassa egy Azure fájlok megosztáson ahhoz, hogy a munkaterület csatlakoztatása bármelyik olyan gépen home vagy notebook címtárat. (Ha szoros fájlszintű engedélyekre van szükség, szüksége lesz egy vagy több virtuális gépeken futó NFS.)

   a. [Hozzon létre egy Azure fájlok megosztás](../../storage/files/storage-how-to-create-file-share.md).
    
   b. A Linux DSVM csatlakoztassa azt. Ha bejelöli a **Connect** gombra kattint, az Azure fájlokat megosztani az Azure portálon, a parancs futtatása a a Bash rendszerhéjat a Linux DSVM megjelenik tárfiókba. A parancs így néz ki:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
7. Tegyük fel, az Azure-fájlok megosztásra /data/workspace, például csatlakoztatva. Most hozzon létre a könyvtárak a megosztáson található, a felhasználók vonatkozó: /data/workspace/user1 /data/workspace/user2 és így tovább. Hozzon létre egy `notebooks` könyvtárhoz, az egyes felhasználók munkaterületen. 
8. Hozzon létre a szimbolikus csatolást `notebooks` a `$HOME/userx/notebooks/remote`.   

Meg kell, a felhasználók az Azure-ban üzemeltetett Active Directory-példányban. Az Active Directory hitelesítő adatok használatával felhasználók bejelentkezhet bármely DSVM (SSH vagy JupyterHub), amely az Azure Active Directory tartományi szolgáltatások csatlakozik. A felhasználó munkaterület Azure fájlok megosztáson van, mert a felhasználók rendelkeznek a jegyzetfüzetek és más feladatok bármely DSVM való hozzáférést, ha JupyterHub használatát. 

Az automatikus skálázást használhatja a virtuálisgép-méretezési készlet létrehozásához egy olyan virtuális gépek, amelyek összes csatlakoznak a tartományhoz, ilyen módon, és a megosztott lemez van beállítva. Felhasználók jelentkezzen be a virtuálisgép-méretezési csoportban lévő elérhető gépi, és hozzáférhetnek a megosztott lemez a notebookok menteni. 

## <a name="next-steps"></a>További lépések

* [Biztonságosan tárolni a felhőben lévő erőforrások eléréséhez szükséges hitelesítő adatokat](dsvm-secure-access-keys.md)



