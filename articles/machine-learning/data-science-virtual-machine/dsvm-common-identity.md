---
title: Közös identitás beállítása a az adatelemző virtuális gép – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az általános felhasználói fiókok, amelyek között több adatelemző virtuális gépek használhatók. Azure Active Directory vagy egy helyszíni Active Directory használatával hitelesítheti a felhasználókat, a Data Science virtuális gép.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés, a csoportos adatelemzési folyamat
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 6be7c63d3879c7ed89cd97eaecd6d59b6b5aadd4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075471"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>A Data Science virtuális gépen közös identitás beállítása

Egy Azure virtuális gép (VM), az adatelemzési virtuális gép (DSVM), beleértve a helyi felhasználói fiókokat hozhat létre a virtuális gép kiépítése során. A felhasználók ezután hitelesíthetők a virtuális gép ezen hitelesítő adatok használatával. Ha több virtuális gép elérését igénylő, ez a módszer gyors bevezetés nehézkes, hitelesítő adatok kezelése. Általános felhasználói fiókokhoz és a egy szabványalapú identitásszolgáltató-kezelési lehetővé teszi több erőforrást az Azure-ban, beleértve a több Dsvm eléréséhez hitelesítő adatok egy készletét használja. 

Active Directory egy népszerű identitásszolgáltatóktól szolgáltató, és egy szolgáltatás és a helyszínen is támogatott az Azure-ban. Használhatja az Azure Active Directory (Azure AD) vagy a helyszíni Active Directory felhasználók DSVM önálló vagy egy Azure-beli virtuálisgép-méretezési csoportban lévő Dsvm fürttel való hitelesítéséhez. Ehhez a DSVM-példányok egy Active Directory-tartományhoz történő csatlakoztatásához. 

Ha már rendelkezik Active Directory az identitások kezelésére, használhatja az általános identitás-szolgáltatóként. Ha nem rendelkezik az Active Directory, futtathatja felügyelt Active Directory-példányok az Azure-ban egy úgynevezett szolgáltatáson keresztül [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

A dokumentációban [Azure ad-ben](https://docs.microsoft.com/azure/active-directory/) biztosít részletes [felügyeleti utasításokat](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity), beleértve az Azure ad-ben csatlakozhat a helyszíni címtár, ha rendelkezik ilyennel. 

Ez a cikk ismerteti a lépéseket az Azure AD DS használatával egy teljes körűen felügyelt Active Directory tartományi szolgáltatáshoz az Azure-ban beállított. A Dsvm majd csatlakoztatása a felügyelt Active Directory-tartomány engedélyezése a felhasználók férhetnek hozzá a készlet Dsvm (és más Azure-erőforrások) közös felhasználói fiók és a hitelesítő adatok használatával. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Egy teljes körűen felügyelt, az Azure Active Directory-tartomány beállítása

Az Azure Active Directory tartományi szolgáltatások leegyszerűsíti az identitások kezelése egy teljes körűen felügyelt szolgáltatás azáltal, hogy az Azure-ban. Az Active Directory-tartomány kezelheti a felhasználókat és csoportokat. A lépések egy Azure-ban üzemeltetett Active Directory tartományt és felhasználói fiókot a címtárban beállításához a következők:

1. Az Azure Portalon vegye fel a felhasználót az Active Directory: 

   a. Jelentkezzen be az [Azure Active Directory felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
    
   b. Válassza az **Azure Active Directory**, majd a **Felhasználók és csoportok** elemet.
    
   c. A **Felhasználók és csoportok** területen válassza a **Minden felhasználó**, majd az **Új felhasználó** lehetőséget.
   
      A **felhasználói** panel nyílik meg.
      
      ![A "Felhasználó" panelen](./media/add-user.png)
    
   d. Adja meg a felhasználó adatait (például **Név** és **Felhasználónév**). A tartomány felhasználónév részét kell lennie, vagy a kezdeti alapértelmezett tartomány neve "[domain name].onmicrosoft.com", vagy olyan ellenőrzött, nem összevont [egyéni tartománynév](../../active-directory/add-custom-domain.md) például "contoso.com".
    
   e. Másolja a vágólapra vagy egyéb módon jegyezze fel a létrehozott jelszót, hogy megadhassa azt a felhasználónak a folyamat befejezése után.
    
   f. Az adatokat a felhasználóhoz tartozó **Profil**, **Csoportok**, vagy **Címtárbeli szerepkör** területeken is megadhatja. 
    
   g. A **Felhasználó** területen válassza a **Létrehozás** elemet.
    
   h. Küldje el a létrehozott jelszót biztonságosan az új felhasználónak, hogy az be tudjon vele jelentkezni.

1. Hozzon létre egy Azure AD DS-példánya. Kövesse a cikk a [engedélyezése az Active Directory Domain Servicest az Azure portal használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (1-5 feladat). Fontos a meglévő felhasználói jelszavakat az Active Directory frissítése, hogy a jelszó az Azure Active Directory tartományi szolgáltatásokban szinkronizálva van. Fontos továbbá DNS hozzáadása az Azure Active Directory tartományi szolgáltatások, 4. feladat, a cikkben leírtak szerint. 

1. Hozzon létre egy külön DSVM-alhálózat a 2. feladat az előző lépésben létrehozott virtuális hálózat.
1. Hozzon létre egy vagy több adatelemző virtuális gép példány a DSVM-alhálózat. 
1. Kövesse a [utasításokat](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) DSVM hozzáadása az Active Directory. 
1. Csatlakoztassa az Azure Files megosztáson a home vagy a jegyzetfüzet címtár csatlakoztatása a munkaterület bármelyik olyan gépen engedélyezi. (Ha szoros fájl szintű engedélyek szükségesek, szüksége lesz egy vagy több virtuális gépeken futó NFS.)

   a. [Hozzon létre egy Azure-fájlmegosztási](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Csatlakoztathatom azokat az Linux-DSVM. Amikor kiválasztja a **Connect** esetében az Azure Files oszthat meg a storage-fiókot az Azure Portalon, a parancs futtatása a a Bash rendszerhéjat a Linux-DSVM jelenik meg a gombot. A parancs így néz ki:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Tegyük fel, az Azure-fájlmegosztást az /data/workspace, például csatlakoztatva. Most hozzon létre a könyvtárak a felhasználók a megosztásban található minden egyes: /data/workspace/user1 /data/workspace/user2 és így tovább. Hozzon létre egy `notebooks` könyvtárban lévő minden egyes felhasználó munkaterületét. 
1. Hozzon létre szimbolikus hivatkozásokat `notebooks` a `$HOME/userx/notebooks/remote`.   

Már a felhasználók az Azure Active Directory-példányában. Az Active Directory hitelesítő adatok használatával felhasználók bejelentkezhetnek bármely DSVM (SSH- vagy JupyterHub), amely csatlakozik az Azure Active Directory tartományi Szolgáltatásokban való. Mivel a felhasználói munkaterület egy Azure-fájlmegosztással, felhasználók bármilyen dsvm-hez a a jegyzetfüzetekre és más munkahelyi hozzáféréssel rendelkeznek a JupyterHub használatát. 

Az automatikus skálázáshoz a készlet összes a tartományhoz csatlakozó ilyen módon, és a megosztott lemezzel, csatlakoztatott virtuális gépek létrehozása egy virtuális gép méretezési is használhatja. Felhasználók jelentkezzen be a virtuálisgép-méretezési csoportban lévő bármely elérhető gépére, és hozzáférhetnek a megosztott lemez a jegyzetfüzetekre mentési helye. 

## <a name="next-steps"></a>További lépések

* [A felhőbeli erőforrások eléréséhez szükséges hitelesítő adatok biztonságos tárolása](dsvm-secure-access-keys.md)



