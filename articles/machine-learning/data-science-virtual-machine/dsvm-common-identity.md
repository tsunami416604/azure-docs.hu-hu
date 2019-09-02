---
title: Közös identitás beállítása
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan hozhat létre az általános felhasználói fiókok, amelyek között több adatelemző virtuális gépek használhatók. Azure Active Directory vagy egy helyszíni Active Directory használatával hitelesítheti a felhasználókat, a Data Science virtuális gép.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés, a csoportos adatelemzési folyamat
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208142"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Közös identitás beállítása Data Science Virtual Machine

Microsoft Azure virtuális gépen (VM), beleértve a Data Science Virtual Machinet (DSVM), helyi felhasználói fiókokat hozhat létre a virtuális gép kiépítés közben. A felhasználók ezután hitelesíthetők a virtuális gép ezen hitelesítő adatok használatával. Ha több virtuális gépre van szüksége a felhasználóknak, a hitelesítő adatok kezelése nagyon nehézkes lehet. Kiváló megoldás, ha általános felhasználói fiókokat telepít, és a szabványon alapuló identitás-szolgáltatón keresztül végzi a felügyeletet. Ezzel a módszerrel egyetlen hitelesítő adat használatával férhet hozzá több erőforráshoz az Azure-ban, többek között több Dsvm is.

A Active Directory egy népszerű identitás-szolgáltató, és az Azure-ban is támogatott, felhőalapú szolgáltatásként és helyszíni címtárként. Használhatja az Azure Active Directory (Azure AD) vagy a helyszíni Active Directory felhasználók DSVM önálló vagy egy Azure-beli virtuálisgép-méretezési csoportban lévő Dsvm fürttel való hitelesítéséhez. Ehhez a DSVM-példányok egy Active Directory-tartományhoz történő csatlakoztatásához.

Ha már rendelkezik Active Directory, használhatja azt a közös identitás-szolgáltatóként. Ha nem rendelkezik Active Directoryval, [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS) használatával futtathat egy felügyelt Active Directory-példányt az Azure-ban.

Az [Azure ad](https://docs.microsoft.com/azure/active-directory/) dokumentációja részletes útmutatást nyújt, beleértve az Azure ad és a helyszíni címtár összekapcsolására vonatkozó útmutatást, ha van ilyen. [](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

Ez a cikk azt ismerteti, hogyan állítható be egy teljes körűen felügyelt Active Directory tartományi szolgáltatás az Azure-ban az Azure AD DS használatával. Ezután csatlakozhat a Dsvm a felügyelt Active Directory tartományhoz. Ez a megközelítés lehetővé teszi a felhasználók számára, hogy a Dsvm (és más Azure-erőforrások) egy közös felhasználói fiókkal és hitelesítő adatokkal férhessenek hozzá.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Egy teljes körűen felügyelt, az Azure Active Directory-tartomány beállítása

Az Azure Active Directory tartományi szolgáltatások leegyszerűsíti az identitások kezelése egy teljes körűen felügyelt szolgáltatás azáltal, hogy az Azure-ban. Az Active Directory-tartomány kezelheti a felhasználókat és csoportokat. Az Azure által üzemeltetett Active Directory tartományi és felhasználói fiókok a címtárban történő beállításához kövesse az alábbi lépéseket:

1. Az Azure Portalon vegye fel a felhasználót az Active Directory: 

   1. Jelentkezzen be a [Azure Active Directory felügyeleti](https://aad.portal.azure.com) központba egy olyan fiókkal, amely a címtár globális rendszergazdája.
    
   1. Válassza az **Azure Active Directory**, majd a **Felhasználók és csoportok** elemet.
    
   1. A **felhasználók és csoportok**területen válassza a **minden felhasználó**lehetőséget, majd válassza az **új felhasználó**lehetőséget.
   
           The **User** pane opens:
      
      ![A "Felhasználó" panelen](./media/add-user.png)
    
   1. Adja meg a felhasználó adatait (például **Név** és **Felhasználónév**). A tartomány felhasználónév részét kell lennie, vagy a kezdeti alapértelmezett tartomány neve "[domain name].onmicrosoft.com", vagy olyan ellenőrzött, nem összevont [egyéni tartománynév](../../active-directory/add-custom-domain.md) például "contoso.com".
    
   1. Másolja a vágólapra vagy egyéb módon jegyezze fel a létrehozott jelszót, hogy megadhassa azt a felhasználónak a folyamat befejezése után.
    
   1. Az adatokat a felhasználóhoz tartozó **Profil**, **Csoportok**, vagy **Címtárbeli szerepkör** területeken is megadhatja. 
    
   1. A **felhasználó**területen válassza a **Létrehozás**elemet.
    
   1. Biztonságosan továbbítsa a generált jelszót az új felhasználónak, hogy be tudja jelentkezni.

1. Hozzon létre egy Azure AD DS-példánya. Kövesse a [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (az "példány létrehozása és alapszintű beállítások konfigurálása" című szakaszt). Fontos a meglévő felhasználói jelszavakat az Active Directory frissítése, hogy a jelszó az Azure Active Directory tartományi szolgáltatásokban szinkronizálva van. Fontos továbbá, hogy a DNS-t az Azure AD DS-hoz adja hozzá, ahogy az a következő szakaszban is látható: "a mezők befejezése a Azure Portal alapjai ablakban az Azure AD DS-példány létrehozásakor" című részében leírtak szerint.

1. Hozzon létre egy különálló DSVM alhálózatot az előző lépés "virtuális hálózat létrehozása és konfigurálása" szakaszában létrehozott virtuális hálózaton.
1. Hozzon létre egy vagy több DSVM-példányt a DSVM-alhálózatban.
1. Az [utasításokat](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) követve adja hozzá a DSVM a Active Directoryhoz. 
1. Csatlakoztassa egy Azure Files-megosztást a saját otthoni vagy jegyzetfüzet-címtárának üzemeltetéséhez, hogy a munkaterület bármely gépen csatlakoztatható legyen. (Ha szűk fájl szintű engedélyekre van szüksége, egy vagy több virtuális gépen futó hálózati fájlrendszerre [NFS-re] van szükség.)

   1. [Hozzon létre egy Azure-fájlmegosztási](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Csatlakoztassa ezt a megosztást a Linux-DSVM. Ha a Azure Portal a Storage-fiókban a **Csatlakozás** a Azure Files megosztáshoz lehetőséget választja, megjelenik a Linux DSVM bash rendszerhéjában futtatandó parancs. A parancs így néz ki:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Tegyük fel például, hogy csatlakoztatta a Azure Files-megosztást a/Data/Workspace.-ben Most hozzon létre címtárakat az egyes felhasználók számára a megosztásban:/Data/Workspace/user1,/Data/Workspace/USER2 stb. Hozzon létre egy `notebooks` könyvtárban lévő minden egyes felhasználó munkaterületét. 
1. Hozzon létre szimbolikus hivatkozásokat `notebooks` a `$HOME/userx/notebooks/remote`.   

Most már rendelkezik az Azure-ban üzemeltetett Active Directory-példány felhasználóinak. Active Directory hitelesítő adatok használatával a felhasználók bejelentkezhetnek az Azure AD DShoz csatlakoztatott bármely DSVM (SSH vagy JupyterHub). Mivel a felhasználói munkaterület egy Azure-fájlmegosztással, felhasználók bármilyen dsvm-hez a a jegyzetfüzetekre és más munkahelyi hozzáféréssel rendelkeznek a JupyterHub használatát.

Az automatikus skálázáshoz a készlet összes a tartományhoz csatlakozó ilyen módon, és a megosztott lemezzel, csatlakoztatott virtuális gépek létrehozása egy virtuális gép méretezési is használhatja. A felhasználók bejelentkezhetnek bármely elérhető gépre a virtuálisgép-méretezési csoportba, és hozzáférhetnek a megosztott lemezhez, amelyen a jegyzetfüzetek el vannak mentve. 

## <a name="next-steps"></a>További lépések

* [A felhőbeli erőforrások eléréséhez szükséges hitelesítő adatok biztonságos tárolása](dsvm-secure-access-keys.md)



