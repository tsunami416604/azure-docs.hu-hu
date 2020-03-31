---
title: Tartományfüggetlen munkacsoport-elérhetőségi csoport konfigurálása
description: Megtudhatja, hogy miként konfigurálhat egy Active Directory tartományfüggetlen munkacsoportot, amely mindig elérhető helyen áll rendelkezésre állási csoportban az Azure-beli SQL Server virtuális gépen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122674"
---
# <a name="configure-a-workgroup-availability-group"></a>Munkacsoport-elérhetőségi csoport konfigurálása 

Ez a cikk ismerteti az Active Directory tartományfüggetlen fürt jének létrehozásához szükséges lépéseket egy mindig elérhető állási csoporttal; ezt munkacsoport-fürtnek is nevezik. Ez a cikk a munkacsoport és a rendelkezésre állási csoport előkészítéséhez és konfigurálásához szükséges lépésekre összpontosít, és elfedi a más cikkekben szereplő lépéseket, például a fürt létrehozásának vagy az elérhetőségi csoport üzembe helyezésének lépéseit. 


## <a name="prerequisites"></a>Előfeltételek

A munkacsoport elérhetőségi csoportjának konfigurálásához a következőkre van szükség:
- Legalább két SQL Server 2016 (vagy újabb) SQL Server 2016 (vagy újabb) rendszert futtató, statikus IP-címeket használó, azonos rendelkezésre állási csoportba telepített vagy különböző rendelkezésre állási zónákba telepített virtuális gép. 
- Az alhálózaton legalább 4 szabad IP-címmel rendelkező helyi hálózat. 
- A rendszergazdai csoport minden egyes gépén lévő olyan fiók, amely rendszergazdai jogosultságokkal is rendelkezik az SQL Server kiszolgálón belül. 
- Nyitott portok: TCP 1433, TCP 5022, TCP 59999. 

Referenciaként a következő paramétereket használja ebben a cikkben, de szükség szerint módosíthatók: 

| **Név** | **Paraméter** |
| :------ | :---------------------------------- |
| **1. csomópont**   | AGNode1 (10.0.0.4) |
| **2. csomópont**   | AGNode2 (10.0.0.5) |
| **Fürt neve** | AGWGAG (10.0.0.6) |
| **Figyelő** | AGListener (10.0.0.7) | 
| **DNS-utótag** | ag.wgcluster.example.com | 
| **A munkacsoport neve** | AGWorkgroup (AGWorkgroup) | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>DNS-utótag beállítása 

Ebben a lépésben konfigurálja a DNS-utótagot mindkét kiszolgálóhoz. Például: `ag.wgcluster.example.com`. Ez lehetővé teszi annak az objektumnak a nevét, amelyhez csatlakozni szeretne, `AGNode1.ag.wgcluster.example.com`mint a hálózaton belüli teljesen minősített címet, például . 

A DNS-utótag konfigurálásához hajtsa végre az alábbi lépéseket:

1. RDP az első csomópontba, és nyissa meg a Kiszolgálókezelőt. 
1. Válassza a **Helyi kiszolgáló** lehetőséget, majd a Számítógép neve csoportban válassza ki a virtuális gép **nevét.** 
1. Válassza a **Módosítás...** lehetőséget **a számítógép átnevezéséhez...**. 
1. A munkacsoport nevének módosítása értelmesnek, például: `AGWORKGROUP` 

   ![Munkacsoport nevének módosítása](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Válassza az **Egyebek lehetőséget...** a **DNS-utótag és a NetBIOS számítógépnév** párbeszédpanel megnyitásához. 
1. Írja be a DNS-utótag nevét **a számítógép Elsődleges DNS-utótagja**mezőbe, `ag.wgcluster.example.com` majd válassza az OK **gombot:** 

   ![DNS-utótag hozzáadása](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Ellenőrizze, hogy a **Teljes számítógépnév** most már a DNS-utótagot mutatja-e, majd a módosítások mentéséhez kattintson az **OK gombra:** 

   ![DNS-utótag hozzáadása](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Indítsa újra a kiszolgálót, amikor a rendszer erre kéri. 
1. Ismételje meg ezeket a lépéseket a rendelkezésre állási csoporthoz használandó bármely más csomóponton. 

## <a name="edit-host-file"></a>Gazdafájl szerkesztése

Mivel nincs active directory, nincs mód a Windows-kapcsolatok hitelesítésére. Így rendeljen hozzá megbízhatósági kapcsolatot a gazdafájl szövegszerkesztővel való szerkesztésével. 

A gazdafájl szerkesztéséhez hajtsa végre az alábbi lépéseket:

1. RDP a virtuális gépbe. 
1. A **Fájlkezelővel** `c:\windows\system32\drivers\etc`nyissa meg a programot. 
1. Kattintson a jobb gombbal a **hosts** fájlra, és nyissa meg a fájlt a **Jegyzettömbdel** (vagy bármely más szövegszerkesztővel).
1. A fájl végén adjon hozzá egy bejegyzést minden csomóponthoz, a rendelkezésre állási `IP Address, DNS Suffix #comment` csoporthoz és a figyelőhez hasonló formában: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Bejegyzések hozzáadása az IP-címhez, a fürthöz és a figyelőhöz a gazdafájlhoz](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Engedélyek beállítása

Mivel az engedélyek kezeléséhez nincs Active Directory címtár, manuálisan engedélyeznie kell egy nem beépített helyi rendszergazdai fióknak a fürt létrehozását. 

Ehhez futtassa a következő PowerShell-parancsmast egy felügyeleti PowerShell-munkamenetben minden csomóponton: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>A feladatátvevő fürt létrehozása

Ebben a lépésben hozza létre a feladatátvevő fürt. Ha nem ismeri ezeket a lépéseket, kövesse őket a [feladatátvevő fürt oktatóanyagából.](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct)

Figyelemre méltó különbségek az oktatóanyag és a munkacsoport-fürt esetében a tennivalók között:
- Törölje a jelet a **Storage**és **a Storage Spaces Direct jelölőnégyzetből** a fürtérvényesítés futtatásakor. 
- Amikor hozzáadja a csomópontokat a fürthöz, adja hozzá a teljesen minősített nevet, például:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Törölje a jelet **az Összes jogosult tároló hozzáadása a fürthöz jelölőnégyzetből.** 

A fürt létrehozása után rendeljen hozzá egy statikus fürt IP-címet. Ehhez kövesse az alábbi lépéseket:

1. Az egyik csomóponton nyissa meg a **Feladatátvevőfürt-kezelőt,** jelölje ki a fürtöt, kattintson a jobb gombbal a ** \<ClusterNam>néven** a **Fürtalapvető erőforrások csoportban,** majd válassza a **Tulajdonságok parancsot.** 

   ![A fürtnév tulajdonságainak elindítása](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Jelölje ki az IP-cím csoportban az **IP-címet,** és válassza **a Szerkesztés**lehetőséget. 
1. Válassza **a Statikus használata**lehetőséget, adja meg a fürt IP-címét, majd válassza az **OK**gombot: 

   ![Statikus IP-cím biztosítása a fürthöz](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Ellenőrizze, hogy a beállítások helyesek-e, majd a mentésükhöz válassza az **OK gombot:**

   ![Fürttulajdonságainak ellenőrzése](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Felhőbeli tanúsító létrehozása 

Ebben a lépésben konfiguráljon egy felhőmegosztási tanúsítót. Ha nem ismeri a lépéseket, tekintse meg a [feladatátvevő fürt oktatóanyagát.](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness) 

## <a name="enable-availability-group-feature"></a>Rendelkezésre állási csoport szolgáltatás engedélyezése 

Ebben a lépésben engedélyezze a rendelkezésre állási csoport funkciót. Ha nem ismeri a lépéseket, tekintse meg a [rendelkezésre állási csoport oktatóanyagát.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups) 

## <a name="create-keys-and-certificate"></a>Kulcsok és tanúsítványok létrehozása

Ebben a lépésben hozzon létre tanúsítványokat, amelyeket az SQL bejelentkezési adatok a titkosított végponton használnak. Hozzon létre egy mappát minden csomóponton a `c:\certs`tanúsítvány biztonsági másolatainak tárolására, például . 

Az első csomópont konfigurálásához kövesse az alábbi lépéseket: 

1. Nyissa meg **az SQL Server Management Studio alkalmazást,** és csatlakozzon az első csomóponthoz, például `AGNode1`ahoz. 
1. Nyisson meg egy **Új lekérdezés** ablakot, és futtassa a következő Transact-SQL (T-SQL) utasítást, miután frissített egy összetett és biztonságos jelszóra:

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

1. Ezután hozza létre a HADR-végpontot, és használja a tanúsítványt a hitelesítéshez a Transact-SQL (T-SQL) utasítás futtatásával:

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

1. A **Fájlkezelővel** lépjen a tanúsítvány helyételő `c:\certs`fájlhelyre, például . 
1. Készítsen másolatot a tanúsítványról, `AGNode1Cert.crt`például az első csomópontról, és vigye át ugyanarra a helyre a második csomóponton. 

A második csomópont konfigurálásához kövesse az alábbi lépéseket: 

1. Csatlakozzon a második csomóponthoz az SQL `AGNode2`Server Management **Studio**segítségével, például . 
1. Az **Új lekérdezés** ablakban futtassa a következő Transact-SQL (T-SQL) utasítást, miután frissített egy összetett és biztonságos jelszóra: 

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

1. Ezután hozza létre a HADR-végpontot, és használja a tanúsítványt a hitelesítéshez a Transact-SQL (T-SQL) utasítás futtatásával:

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

1. A **Fájlkezelővel** lépjen a tanúsítvány helyételő `c:\certs`fájlhelyre, például . 
1. Készítsen másolatot a tanúsítványról, `AGNode2Cert.crt`például a második csomópontról, és vigye át ugyanarra a helyre az első csomóponton. 

Ha a fürtben más csomópontok is vannak, ismételje meg ezeket a lépéseket a megfelelő tanúsítványnevek módosításával. 

## <a name="create-logins"></a>Bejelentkezések létrehozása

A tanúsítványhitelesítés az adatok csomópontok közötti szinkronizálására szolgál. Ennek engedélyezéséhez hozzon létre egy bejelentkezési kapcsolatot a másik csomóponthoz, hozzon létre egy felhasználót a bejelentkezéshez, hozzon létre egy tanúsítványt a bejelentkezéshez a biztonsági tanúsítvány használatához, majd adjon meg kapcsolódási pontot a tükrözési végponton. 

Ehhez először futtassa a következő Transact-SQL (T-SQL) lekérdezést `AGNode1`az első csomóponton, például: 

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

Ezután futtassa a következő Transact-SQL (T-SQL) lekérdezést a második csomóponton, például: `AGNode2` 

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

Ha a fürtben más csomópontok is vannak, ismételje meg ezeket a lépéseket a megfelelő tanúsítvány és felhasználónevek módosításával. 

## <a name="configure-availability-group"></a>Rendelkezésre állási csoport konfigurálása

Ebben a lépésben konfigurálja az elérhetőségi csoportot, és adja hozzá az adatbázisokat. Ne hozzon létre egy hallgató ebben az időben. Ha nem ismeri a lépéseket, tekintse meg a [rendelkezésre állási csoport oktatóanyagát.](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group) Győződjön meg arról, hogy kezdeményezzen feladatátvételt és feladat-visszavételt annak ellenőrzéséhez, hogy minden úgy működik-e, ahogy kellene. 

   > [!NOTE]
   > Ha a szinkronizálási folyamat során hiba történik, előfordulhat, `NT AUTHORITY\SYSTEM` hogy rendszergazdai jogokat kell biztosítania a fürterőforrások létrehozásához az első csomóponton, például `AGNode1` ideiglenesen. 

## <a name="configure-load-balancer"></a>Terheléselosztó konfigurálása

Ebben az utolsó lépésben konfigurálja a terheléselosztót az [Azure Portal](virtual-machines-windows-portal-sql-alwayson-int-listener.md) vagy a [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) használatával


## <a name="next-steps"></a>Következő lépések

Az SQL [Virtuálisgép CLI](virtual-machines-windows-sql-availability-group-cli.md) használatával is konfigurálhat egy rendelkezésre állási csoportot. 


