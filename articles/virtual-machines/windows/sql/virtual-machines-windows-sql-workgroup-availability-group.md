---
title: Tartományi független munkacsoport rendelkezésre állási csoportjának konfigurálása
description: Megtudhatja, hogyan konfigurálhat egy Active Directory-tartomány-független munkacsoportot always on rendelkezésre állási csoportot egy SQL Server virtuális gépen az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77122674"
---
# <a name="configure-a-workgroup-availability-group"></a>Munkacsoport rendelkezésre állási csoportjának konfigurálása 

Ez a cikk ismerteti azokat a lépéseket, amelyek szükségesek egy Active Directory tartományhoz független fürt létrehozásához egy always on rendelkezésre állási csoporttal; ezt munkacsoport-fürtnek is nevezzük. Ez a cikk a munkacsoportok és a rendelkezésre állási csoport előkészítéséhez és konfigurálásához szükséges lépéseket, valamint a más cikkekben tárgyalt lépéseket, például a fürt létrehozását, illetve a rendelkezésre állási csoport központi telepítését ismerteti. 


## <a name="prerequisites"></a>Előfeltételek

Munkacsoporton rendelkezésre állási csoport konfigurálásához a következőkre lesz szüksége:
- Legalább két Windows Server 2016 (vagy újabb) rendszerű virtuális gép, amely SQL Server 2016 (vagy újabb) rendszert futtat, ugyanarra a rendelkezésre állási csoportba, vagy különböző rendelkezésre állási zónákba helyezi üzembe statikus IP-címek használatával. 
- Egy helyi hálózat, amely legalább 4 ingyenes IP-címmel rendelkezik az alhálózaton. 
- Egy fiók a rendszergazda csoport minden olyan gépén, amely rendszergazdai jogosultságokkal is rendelkezik SQL Serveron belül. 
- Nyitott portok: TCP 1433, TCP 5022, TCP 59999. 

A következő paraméterek használhatók a cikkben, de a szükséges módon módosítható: 

| **Name (Név)** | **Paraméter** |
| :------ | :---------------------------------- |
| **Csomópont1**   | AGNode1 (10.0.0.4) |
| **Csomópont2**   | AGNode2 (10.0.0.5) |
| **Fürt neve** | AGWGAG (10.0.0.6) |
| **Hallgató** | AGListener (10.0.0.7) | 
| **DNS-utótag** | ag.wgcluster.example.com | 
| **Munkacsoport neve** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>DNS-utótag beállítása 

Ebben a lépésben konfigurálja mindkét kiszolgáló DNS-utótagját. Például: `ag.wgcluster.example.com`. Ez lehetővé teszi annak az objektumnak a nevét, amelyhez a hálózaton belül teljesen minősített címként szeretne csatlakozni, például `AGNode1.ag.wgcluster.example.com`. 

A DNS-utótag konfigurálásához kövesse az alábbi lépéseket:

1. Az RDP-t az első csomópontba, majd nyissa meg a Kiszolgálókezelő alkalmazást. 
1. Válassza a **helyi kiszolgáló** lehetőséget, majd válassza ki a virtuális gép nevét a **számítógép neve**alatt. 
1. A **számítógép átnevezéséhez**kattintson a **módosítás..** . elemre... 
1. Módosítsa a munkacsoport nevének nevét úgy, hogy az legyen értelmes, például `AGWORKGROUP`: 

   ![Munkacsoport nevének módosítása](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Válassza a **továbbiak...** lehetőséget a **DNS-utótag és a NetBIOS-számítógépnév** párbeszédpanel megnyitásához. 
1. Írja be a DNS-utótag nevét a **számítógép elsődleges DNS-utótagja**mezőbe, például `ag.wgcluster.example.com`, majd válassza **az OK gombot**: 

   ![DNS-utótag hozzáadása](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Győződjön meg arról, hogy a **teljes számítógépnév** mostantól a DNS-utótagot jeleníti meg, majd kattintson **az OK** gombra a módosítások mentéséhez: 

   ![DNS-utótag hozzáadása](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Ha a rendszer kéri, indítsa újra a kiszolgálót. 
1. Ismételje meg ezeket a lépéseket a rendelkezésre állási csoporthoz használni kívánt többi csomóponton. 

## <a name="edit-host-file"></a>Gazdagép fájljának szerkesztése

Mivel nincs Active Directory, a Windows-kapcsolatok hitelesítésére nincs mód. Ennek megfelelően rendeljen megbízhatóságot úgy, hogy a gazdagép fájlját szövegszerkesztővel szerkeszti. 

A gazda fájl szerkesztéséhez kövesse az alábbi lépéseket:

1. RDP-t a virtuális gépre. 
1. A **fájlkezelő** használatával lépjen `c:\windows\system32\drivers\etc`. 
1. Kattintson a jobb gombbal a **hosts** fájlra, és nyissa meg a fájlt a **Jegyzettömbben** (vagy bármilyen más szövegszerkesztőben).
1. A fájl végén adjon hozzá egy bejegyzést az egyes csomópontokhoz, a rendelkezésre állási csoporthoz és a figyelőhöz `IP Address, DNS Suffix #comment` például a következő formában: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Adja hozzá az IP-címhez, a fürthöz és a figyelőhöz tartozó bejegyzéseket a gazdagép fájljához](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Engedélyek beállítása

Mivel nincs Active Directory az engedélyek kezeléséhez, manuálisan engedélyeznie kell egy nem beépített helyi rendszergazdai fiókot a fürt létrehozásához. 

Ehhez futtassa a következő PowerShell-parancsmagot egy felügyeleti PowerShell-munkamenetben minden csomóponton: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

Ebben a lépésben a feladatátvevő fürtöt fogja létrehozni. Ha nem ismeri ezeket a lépéseket, követheti őket a [feladatátvevő fürt oktatóanyagában](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Jelentős eltérések az oktatóanyag és a munkacsoporthoz tartozó fürt esetében elvégzendő teendők között:
- Törölje a **tárolót**, és **közvetlen tárolóhelyek** a fürt érvényesítésének futtatásakor. 
- Ha a csomópontokat hozzáadja a fürthöz, adja hozzá a teljes nevet, például:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Törölje **az összes megfelelő tároló hozzáadása a fürthöz**jelölőnégyzet jelölését. 

A fürt létrehozása után rendeljen hozzá egy statikus fürt IP-címét. Ehhez kövesse az alábbi lépéseket:

1. Az egyik csomóponton nyissa meg a **Feladatátvevőfürt-kezelőt**, válassza ki a fürtöt, kattintson a jobb gombbal a **névre: \<ClusterNam >** a **fürt alapvető erőforrásai** területen, majd válassza a **Tulajdonságok**lehetőséget. 

   ![A fürt nevének megnyitása](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Válassza ki az IP-címet az **IP-címek** területen, és válassza a **Szerkesztés**lehetőséget. 
1. Válassza a **statikus használata**lehetőséget, adja meg a fürt IP-címét, majd kattintson **az OK gombra**: 

   ![Statikus IP-cím megadása a fürt számára](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Győződjön meg arról, hogy a beállítások helyesek, majd kattintson **az OK** gombra a mentéshez:

   ![Fürt tulajdonságainak ellenőrzése](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Felhőbeli tanúsító létrehozása 

Ebben a lépésben konfigurálja a tanúsító Felhőbeli megosztást. Ha még nem ismeri a lépéseket, tekintse meg a [feladatátvevő fürt oktatóanyagát](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Rendelkezésre állási csoport funkciójának engedélyezése 

Ebben a lépésben engedélyezze a rendelkezésre állási csoport szolgáltatást. Ha még nem ismeri a lépéseket, tekintse meg a [rendelkezésre állási csoport oktatóanyagát](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Kulcsok és tanúsítvány létrehozása

Ebben a lépésben olyan tanúsítványokat hoz létre, amelyeket az SQL-bejelentkezés a titkosított végponton használ. Hozzon létre egy mappát az egyes csomópontokon a tanúsítványok biztonsági másolatainak tárolásához, például `c:\certs`. 

Az első csomópont konfigurálásához kövesse az alábbi lépéseket: 

1. Nyissa meg **SQL Server Management Studio** és kapcsolódjon az első csomóponthoz, például `AGNode1`hoz. 
1. Nyisson meg egy **új lekérdezési** ablakot, és futtassa a következő Transact-SQL (T-SQL) utasítást egy összetett és biztonságos jelszóra való frissítés után:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Ezután hozza létre az HADR-végpontot, és használja a tanúsítványt a hitelesítéshez a Transact-SQL (T-SQL) utasítás futtatásával:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. A **fájlkezelővel** nyissa meg a fájl helyét, ahol a tanúsítvány, például `c:\certs`. 
1. Manuálisan készítsen másolatot a tanúsítványról, például `AGNode1Cert.crt`ről, az első csomópontról, majd vigye át a második csomópont ugyanazon helyére. 

A második csomópont konfigurálásához kövesse az alábbi lépéseket: 

1. Kapcsolódjon a második csomóponthoz **SQL Server Management Studio**, például `AGNode2`. 
1. Egy **új lekérdezési** ablakban futtassa a következő Transact-SQL (T-SQL) utasítást egy összetett és biztonságos jelszóra való frissítés után: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Ezután hozza létre az HADR-végpontot, és használja a tanúsítványt a hitelesítéshez a Transact-SQL (T-SQL) utasítás futtatásával:

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. A **fájlkezelővel** nyissa meg a fájl helyét, ahol a tanúsítvány, például `c:\certs`. 
1. Manuálisan készítsen másolatot a tanúsítványról, például `AGNode2Cert.crt`ről, a második csomópontról, majd vigye át azt ugyanarra a helyre az első csomóponton. 

Ha a fürt bármely más csomóponttal rendelkezik, ismételje meg ezeket a lépéseket is, ha módosítja a megfelelő tanúsítvány nevét. 

## <a name="create-logins"></a>Bejelentkezések létrehozása

A tanúsítvány-hitelesítés a csomópontok közötti adatszinkronizálásra szolgál. Ennek engedélyezéséhez hozzon létre egy bejelentkezési azonosítót a másik csomóponthoz, hozzon létre egy felhasználót a bejelentkezéshez, hozzon létre egy tanúsítványt a bejelentkezéshez a biztonsági másolat készítéséhez, majd engedélyezze a kapcsolódást a tükrözési végponton. 

Ehhez először futtassa a következő Transact-SQL (T-SQL) lekérdezést az első csomóponton, például `AGNode1`: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Ezután futtassa a következő Transact-SQL (T-SQL) lekérdezést a második csomóponton, például `AGNode2`: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Ha a fürt bármely más csomóponttal rendelkezik, ismételje meg ezeket a lépéseket is, és módosítsa a megfelelő tanúsítvány-és felhasználóneveket. 

## <a name="configure-availability-group"></a>Rendelkezésre állási csoport konfigurálása

Ebben a lépésben konfigurálja a rendelkezésre állási csoportot, és adja hozzá az adatbázisokat. Most ne hozzon létre figyelőt. Ha még nem ismeri a lépéseket, tekintse meg a [rendelkezésre állási csoport oktatóanyagát](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Győződjön meg arról, hogy a feladatátvételt és a feladat-visszavételt kezdeményezi annak ellenőrzéséhez, hogy minden megfelelően működik-e. 

   > [!NOTE]
   > Ha a szinkronizálási folyamat során hiba lép fel, előfordulhat, hogy `NT AUTHORITY\SYSTEM` sysadmin jogosultságot kell biztosítania ahhoz, hogy az első csomóponton hozzon létre fürterőforrás-t, például `AGNode1` átmenetileg. 

## <a name="configure-load-balancer"></a>Terheléselosztó konfigurálása

Ebben az utolsó lépésben konfigurálja a terheléselosztó-t a [Azure Portal](virtual-machines-windows-portal-sql-alwayson-int-listener.md) vagy a [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) használatával


## <a name="next-steps"></a>További lépések

A rendelkezésre állási csoport konfigurálásához az [az SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) -t is használhatja. 


