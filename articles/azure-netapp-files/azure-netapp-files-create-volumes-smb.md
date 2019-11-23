---
title: SMB-kötet létrehozása a Azure NetApp Fileshoz | Microsoft Docs
description: Leírja, hogyan lehet SMB-kötetet létrehozni a Azure NetApp Fileshoz.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 94fc4906478e44365d03e9c8eeadd7cb1946a43a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300531"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>SMB-kötet létrehozása az Azure NetApp Files számára

Azure NetApp Files támogatja az NFS-és SMBv3-köteteket. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Ez a cikk bemutatja, hogyan hozhat létre SMBv3-köteteket. Ha NFS-kötetet szeretne létrehozni, tekintse [meg az NFS-kötet létrehozása Azure NetApp Fileshoz](azure-netapp-files-create-volumes.md)című témakört. 

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Az alhálózatot delegálni kell Azure NetApp Files.  
[Alhálózat delegálása Azure NetApp Filesre](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory kapcsolatokra vonatkozó követelmények

 SMB-kötet létrehozása előtt létre kell hoznia Active Directory kapcsolatokat. A Active Directory kapcsolatokra vonatkozó követelmények a következők: 

* A használt rendszergazdai fióknak képesnek kell lennie számítógépfiókok létrehozására a megadható szervezeti egység (OU) elérési úton.  

* A megfelelő portokat meg kell nyitni a megfelelő Windows Active Directory (AD) kiszolgálón.  
    A szükséges portok a következők: 

    |     Szolgáltatás           |     Port     |     Protokoll     |
    |-----------------------|--------------|------------------|
    |    AD Web Services    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Visszhangos válasz    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-név       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    Biztonságos LDAP        |    636       |    TCP           |
    |    Biztonságos LDAP        |    3269      |    TCP           |
    |    W32Time            |    123       |    UDP           |

* A célként megadott Active Directory tartományi szolgáltatások helyének topológiájának meg kell felelnie az ajánlott eljárásoknak, különösen az Azure-VNet, ahol a Azure NetApp Files telepítve van.  

    Egy új vagy meglévő Active Directory-helyhez hozzá kell adni a Azure NetApp Files központilag telepített virtuális hálózat címterület (ahol a tartományvezérlő elérhető a Azure NetApp Files-ben). 

* A megadott DNS-kiszolgálóknak elérhetőknek kell lenniük a Azure NetApp Files [delegált alhálózatán](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) .  

    Lásd: [Azure NetApp Files hálózati tervezéssel kapcsolatos irányelvek](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) a támogatott hálózati topológiák esetében.

    A hálózati biztonsági csoportoknak (NSG) és a tűzfalaknak megfelelően konfigurált szabályokkal kell rendelkezniük a Active Directory és a DNS forgalmi kéréseinek engedélyezéséhez. 

* A Azure NetApp Files delegált alhálózatnak képesnek kell lennie arra, hogy elérje a tartomány összes Active Directory tartományi szolgáltatások (Hozzáadás) tartományvezérlőjét, beleértve az összes helyi és távoli tartományvezérlőt is. Ellenkező esetben a szolgáltatás megszakítása is bekövetkezhet.  

    Ha olyan tartományvezérlővel rendelkezik, amely nem érhető el a Azure NetApp Files delegált alhálózaton keresztül, beküldhet egy Azure-támogatási kérést, hogy a hatókört **globális** (alapértelmezett) **helyről**módosítsa.  Azure NetApp Files csak a tartományvezérlővel kell kommunikálnia azon a helyen, ahol a Azure NetApp Files delegált alhálózati címtartomány található.

    Lásd: [a hely topológiájának megtervezése az](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) ad-helyekre és-szolgáltatásokra vonatkozóan. 

## <a name="create-an-active-directory-connection"></a>Active Directory-kapcsolatok létrehozása

1. A NetApp-fiókból kattintson **Active Directory kapcsolatok**elemre, majd kattintson a **Csatlakozás**elemre.  

    ![Active Directory kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. A csatlakozás Active Directory ablakban adja meg a következő információkat:

    * **Elsődleges DNS**  
        Ez az a DNS, amely szükséges a Active Directory tartományhoz való csatlakozáshoz és az SMB-hitelesítési műveletekhez. 
    * **Másodlagos DNS-**    
        Ez a másodlagos DNS-kiszolgáló a redundáns Name Services biztosításához. 
    * **Tartomány**  
        Ez annak a Active Directory tartományi szolgáltatások a tartományneve, amelyhez csatlakozni szeretne.
    * **SMB-kiszolgáló (számítógépfiók) előtagja**  
        Ez az Active Directory lévő számítógépfiók elnevezési előtagja, amelyet a Azure NetApp Files új fiókok létrehozásához fog használni.

        Ha például a szervezet által a fájlkiszolgálók számára használt elnevezési szabvány a NAS-01, a NAS-02..., a NAS-045, akkor a "NAS" értéket kell megadnia az előtaghoz. 

        A szolgáltatás szükség szerint további számítógép-fiókokat hoz létre Active Directory.

    * **Szervezeti egység elérési útja**  
        Ez a szervezeti egység (OU) LDAP-elérési útja, ahol a rendszer az SMB-kiszolgáló számítógép-fiókjait hozza létre. Ez a szervezeti egység = második szint, OU = első szint. 

        Ha a Azure NetApp Filest használja a Azure Active Directory Domain Services, a szervezeti egység elérési útja `OU=AADDC Computers` a NetApp-fiókhoz tartozó Active Directory konfigurálásakor.
        
    * Hitelesítő adatok, beleértve a **felhasználónevet** és a **jelszót** is

    ![Csatlakozás Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Kattintson a **Csatlakozás** parancsra.  

    Megjelenik a létrehozott Active Directory-kapcsolatok.

    ![Active Directory kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> A Felhasználónév és a jelszó mezőket a Active Directory-kapcsolatok mentése után szerkesztheti. A kapcsolatok mentése után a többi érték nem szerkeszthető. Ha bármilyen más értéket módosítania kell, először törölnie kell minden telepített SMB-kötetet, majd törölnie és újra létre kell hoznia a Active Directory-kapcsolatokat.

## <a name="add-an-smb-volume"></a>SMB-kötet hozzáadása

1. Kattintson a **kötetek** panelre a kapacitási készletek panelen. 

    ![A kötetek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Megjelenik a kötet létrehozása ablak.

3. A kötet létrehozása ablakban kattintson a **Létrehozás** gombra, és adja meg a következő mezők adatait:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötet nevének egyedinek kell lennie az egyes kapacitási készleteken belül. Legalább három karakter hosszúnak kell lennie. Bármely alfanumerikus karaktert használhat.   

        A kötet neveként nem használható `default`.

    * **Kapacitási készlet**  
        Határozza meg azt a kapacitási készletet, amelyben létre szeretné hozni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Itt adhatja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott VNet rendelkeznie kell egy Azure NetApp Files delegált alhálózattal. A Azure NetApp Files szolgáltatás csak ugyanarról a VNet érhető el, vagy egy olyan VNet, amely ugyanabban a régióban található, mint a kötet VNet-társításon keresztül. Az Express Route használatával is elérheti a kötetet a helyszíni hálózatról.   

    * **Alhálózat**  
        Itt adhatja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell Azure NetApp Files. 
        
        Ha nem delegált alhálózatot, a kötet létrehozása lapon kattintson az **új létrehozása** lehetőségre. Ezután az alhálózat létrehozása lapon adja meg az alhálózati adatokat, majd válassza a **Microsoft. NetApp/kötetek** lehetőséget az alhálózat delegálásához Azure NetApp Files számára. Minden VNet csak egy alhálózat delegálható Azure NetApp Filesra.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kattintson a **protokoll** elemre, és hajtsa végre a következő információkat:  
    * Válassza az **SMB** lehetőséget a kötethez tartozó protokoll típusaként. 
    * Válassza ki a **Active Directory** -kapcsolatokat a legördülő listából.
    * Adja meg a megosztott kötet nevét a **megosztási névben**.

    ![SMB protokoll meghatározása](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. A kötet részleteinek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre.  Ezután kattintson a **Létrehozás** gombra az SMB-kötet létrehozásához.

    A létrehozott kötet megjelenik a kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="next-steps"></a>Következő lépések  

* [Kötetek csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [SMB – gyakori kérdések](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Ismerje meg az Azure-szolgáltatások virtuális hálózati integrációját](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Új Active Directory erdő telepítése az Azure CLI-vel](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
