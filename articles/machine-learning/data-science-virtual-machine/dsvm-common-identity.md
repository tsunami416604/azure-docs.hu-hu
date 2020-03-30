---
title: Közös identitás beállítása
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan hozhat létre közös felhasználói fiókokat, amelyek több adatelemzési virtuális gépen is használhatók. Az Azure Active Directory vagy egy helyszíni Active Directory használatával hitelesítheti a felhasználókat az adatelemzési virtuális gép.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208142"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Közös identitás beállítása adatelemzési virtuális gépen

Egy Microsoft Azure virtuális gépen (VM), beleértve a Data Science virtuális gép (DSVM), hozzon létre helyi felhasználói fiókok kiépítése közben a virtuális gép. A felhasználók ezután hitelesítik magukat a virtuális gép en ezekkel a hitelesítő adatokkal. Ha több virtuális gép, amely a felhasználók nak szükségük van a hozzáférés, a hitelesítő adatok kezelése is nagyon nehézkes. Kiváló megoldás a közös felhasználói fiókok és felügyelet üzembe helyezése egy szabványalapú identitásszolgáltatón keresztül. Ezzel a megközelítéssel egyetlen hitelesítő adatok használatával több erőforrást érhet el az Azure-ban, beleértve több DSVM-et is.

Az Active Directory egy népszerű identitásszolgáltató, és az Azure-ban felhőszolgáltatásként és helyszíni címtárként is támogatott. Az Azure Active Directory (Azure AD) vagy a helyszíni Active Directory használatával hitelesítheti a felhasználókat egy önálló DSVM-en vagy dsvms-fürtön egy Azure virtuálisgép-méretezési csoportban. Ehhez csatlakozzon a DSVM-példányok egy Active Directory tartományhoz.

Ha már rendelkezik Active Directoryval, használhatja azt közös identitásszolgáltatóként. Ha nem rendelkezik Active Directory, futtathat egy felügyelt Active Directory-példány az Azure-ban az [Azure Active Directory tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS).

Az [Azure AD](https://docs.microsoft.com/azure/active-directory/) dokumentációja részletes [felügyeleti utasításokat](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)tartalmaz, beleértve az Azure AD helyszíni címtárhoz való csatlakoztatásához vonatkozó útmutatást, ha rendelkezik ilyentel.

Ez a cikk ismerteti, hogyan állíthat be egy teljes körűen felügyelt Active Directory tartományi szolgáltatás az Azure AD DS használatával. Ezután csatlakozhat a DSVM-ekhez a felügyelt Active Directory tartományhoz. Ez a megközelítés lehetővé teszi a felhasználók számára, hogy egy közös felhasználói fiókon és hitelesítő adatokon keresztül hozzáférjenek a DSVM-ek (és más Azure-erőforrások) készletéhez.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Teljes körűen felügyelt Active Directory-tartomány beállítása az Azure-ban

Az Azure AD DS egyszerűvé teszi az identitások kezelését azáltal, hogy teljes körűen felügyelt szolgáltatást nyújt az Azure-ban. Ezen az Active Directory tartományon kezelheti a felhasználókat és a csoportokat. Ha azure-ban üzemeltetett Active Directory-tartományt és felhasználói fiókokat szeretne beállítani a címtárban, kövesse az alábbi lépéseket:

1. Az Azure Portalon adja hozzá a felhasználót az Active Directoryhoz: 

   1. Jelentkezzen be az [Azure Active Directory felügyeleti központjába](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
    
   1. Válassza az **Azure Active Directory**, majd a **Felhasználók és csoportok** elemet.
    
   1. A **Felhasználók és csoportok csoportban**válassza a Minden **felhasználó**lehetőséget, majd az **Új felhasználó**lehetőséget.
   
           The **User** pane opens:
      
      ![A "Felhasználó" ablaktábla](./media/add-user.png)
    
   1. Adja meg a felhasználó adatait (például **Név** és **Felhasználónév**). A felhasználónév tartománynév-részének vagy a "[tartománynév]onmicrosoft.com" kezdeti alapértelmezett tartománynévnek, vagy egy ellenőrzött, nem összevont [egyéni tartománynévnek](../../active-directory/add-custom-domain.md) ( "contoso.com" kell lennie.
    
   1. Másolja a vágólapra vagy egyéb módon jegyezze fel a létrehozott jelszót, hogy megadhassa azt a felhasználónak a folyamat befejezése után.
    
   1. Az adatokat a felhasználóhoz tartozó **Profil**, **Csoportok**, vagy **Címtárbeli szerepkör** területeken is megadhatja. 
    
   1. A **Felhasználó**csoportban válassza a **Létrehozás lehetőséget.**
    
   1. A létrehozott jelszót biztonságosan terjesztheti az új felhasználónak, hogy azok bejelentkezhessenek.

1. Hozzon létre egy Azure AD DS-példányt. Kövesse az [Azure Active Directory tartományi szolgáltatások engedélyezése az Azure Portalon (a](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) "Példány létrehozása és az alapvető beállítások konfigurálása" című szakasz utasításait). Fontos, hogy frissítse a meglévő felhasználói jelszavakat az Active Directoryban, hogy a jelszó az Azure AD DS-ben szinkronizálva van. Fontos az Azure AD DS-hez való hozzáadása is fontos, ahogy azt az ebben a szakaszban a "Töltse ki az Azure Portal Alapjai ablakában az Azure AD DS-példány létrehozásához" című részben leírtak szerint.

1. Hozzon létre egy külön DSVM-alhálózatot az előző lépés "A virtuális hálózat létrehozása és konfigurálása" szakaszában létrehozott virtuális hálózatban.
1. Hozzon létre egy vagy több DSVM-példányt a DSVM-alhálózatban.
1. A DSVM active Directoryhoz való hozzáadásához kövesse az [utasításokat.](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) 
1. Az Azure Files-megosztás csatlakoztatása az otthoni vagy notebook-címtár üzemeltetéséhez, hogy a munkaterület bármely számítógépre csatlakoztatható legyen. (Ha szigorú fájlszintű engedélyekre van szüksége, egy vagy több virtuális gépen kell futtatnia a Hálózati fájlrendszert (NFS).)

   1. [Hozzon létre egy Azure Files share](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Csatlakoztassa ezt a megosztást a Linux DSVM-en. Ha az Azure-fájlok megosztásához az Azure Portalon a **Connect** lehetőséget választja, megjelenik a Linux DSVM-en a bash rendszerhéjban futtatandó parancs. A parancs így néz ki:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Tegyük fel például, hogy az Azure Files-megosztást a /data/workspace-ben csatlakoztatta. Most hozzon létre könyvtárakat a megosztás minden felhasználója számára: /data/workspace/user1, /data/workspace/user2 és így tovább. Hozzon `notebooks` létre egy könyvtárat az egyes felhasználók munkaterületén. 
1. Szimbolikus hivatkozások `notebooks` létrehozása `$HOME/userx/notebooks/remote`a számára a ban   

Most már rendelkezik a felhasználók az Active Directory-példány ban üzemeltetett Az Azure-ban. Az Active Directory hitelesítő adatainak használatával a felhasználók bejelentkezhetnek bármely DSVM-be (SSH vagy JupyterHub), amely az Azure AD DS-hez csatlakozik. Mivel a felhasználói munkaterület egy Azure Files-megosztáson található, a felhasználók a JupyterHub használatakor hozzáférhetnek a jegyzetfüzeteikhez és más munkáihoz bármely DSVM-ből.

Automatikus skálázás, egy virtuális gép méretezési készlet segítségével hozzon létre egy készlet virtuális gépek, amelyek mind csatlakoznak a tartományhoz ilyen módon, és a megosztott lemez csatlakoztatva. A felhasználók bejelentkezhetnek a virtuálisgép-méretezési csoport bármely elérhető gépére, és hozzáférhetnek a megosztott lemezhez, ahová a jegyzetfüzeteiket menti. 

## <a name="next-steps"></a>További lépések

* [A hitelesítő adatok biztonságos tárolása a felhőbeli erőforrások eléréséhez](dsvm-secure-access-keys.md)



