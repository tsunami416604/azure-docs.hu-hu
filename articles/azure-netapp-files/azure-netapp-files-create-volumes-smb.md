---
title: SMB-kötet létrehozása az Azure NetApp-fájlokhoz | Microsoft dokumentumok
description: Bemutatja, hogyan hozhat létre SMB-kötetet az Azure NetApp-fájlokhoz.
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
ms.date: 03/13/2020
ms.author: b-juche
ms.openlocfilehash: b2000c3fd3d64793f797e997d8f3c10eaed5d7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409589"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>SMB-kötet létrehozása az Azure NetApp Files számára

Az Azure NetApp Files támogatja az NFS- és SMBv3-köteteket. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Ez a cikk bemutatja, hogyan hozhat létre Egy SMBv3 kötet. Ha NFS-kötetet szeretne létrehozni, olvassa [el az NFS-kötet létrehozása az Azure NetApp-fájlokhoz című témakört.](azure-netapp-files-create-volumes.md) 

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Az alhálózatot delegálni kell az Azure NetApp-fájlokszámára.  
[Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Az Active Directory-kapcsolatokra vonatkozó követelmények

 SMB-kötet létrehozása előtt létre kell hoznia az Active Directory-kapcsolatokat. Az Active Directory-kapcsolatok ra vonatkozó követelmények a következők: 

* A használt rendszergazdai fióknak képesnek kell lennie a gépfiókok létrehozására a szervezeti egység elérési útján, amelyet meg fog adni.  

* A megfelelő portoknak nyitva kell lenniük a megfelelő Windows Active Directory (AD) kiszolgálón.  
    A szükséges portok a következők: 

    |     Szolgáltatás           |     Port     |     Protocol (Protokoll)     |
    |-----------------------|--------------|------------------|
    |    AD webszolgáltatások    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Visszhang válasz    |
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
    |    w32idő            |    123       |    UDP           |

* A célzott Active Directory tartományi szolgáltatások helytopológiájának be kell tartania az ajánlott eljárásokat, különösen az Azure Virtuális hálózatot, ahol az Azure NetApp-fájlok telepítve van.  

    Az Azure NetApp-fájlokat kiszolgáló virtuális hálózat címterét hozzá kell adni egy új vagy meglévő Active Directory-helyhez (ahol az Azure NetApp Files által elérhető tartományvezérlő érhető el). 

* A megadott DNS-kiszolgálóknak elérhetőnek kell lenniük az Azure NetApp-fájlok [delegált alhálózatáról.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

    A támogatott hálózati topológiákhoz [lásd: Az Azure NetApp Files hálózattervezés irányelvei.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)

    A hálózati biztonsági csoportoknak (NSG-knek) és tűzfalaknak megfelelően konfigurált szabályokkal kell rendelkezniük az Active Directory és a DNS-forgalom kéréseinek engedélyezéséhez. 

* Az Azure NetApp-fájlok delegált alhálózatának képesnek kell lennie a tartomány összes Active Directory tartományi szolgáltatás (ADDS) tartományvezérlőjének elérésére, beleértve az összes helyi és távoli tartományvezérlőt is. Ellenkező esetben a szolgáltatás megszakadhat.  

    Ha olyan tartományvezérlőivel rendelkezik, amelyek et az Azure NetApp Files delegált alhálózata nem érhető el, az Active Directory-kapcsolat létrehozásakor megadhat egy Active Directory-helyet.  Az Azure NetApp Files-nak csak azon a helyen lévő tartományvezérlőkkel kell kommunikálnia, ahol az Azure NetApp Files delegált alhálózati címterülete található.

    Lásd: [A webhelytopológia tervezése az](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) AD-webhelyekről és -szolgáltatásokról. 
    
Tekintse meg az Azure NetApp Files [SMB gyakori kérdéseket](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) a további AD-információkról. 

## <a name="decide-which-domain-services-to-use"></a>A használni használandó tartományi szolgáltatások eldöntése 

Az Azure NetApp-fájlok az [Active Directory tartományi szolgáltatások](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (ADDS) és az Azure Active Directory tartományi szolgáltatások (AADDS) az AD-kapcsolatok.  Az AD-kapcsolat létrehozása előtt el kell döntenie, hogy az ADDS vagy az AADDS lehetőséget használja-e.  

További információ: [Összehasonlítása saját felügyelt Active Directory tartományi szolgáltatások, Az Azure Active Directory és a felügyelt Azure Active Directory tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions). 

### <a name="active-directory-domain-services"></a>Active Directory tartományi szolgáltatások

Használhatja a kívánt [Active Directory helyek és szolgáltatások](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) hatókörét az Azure NetApp-fájlokhoz. Ez a beállítás lehetővé teszi az [Azure NetApp-fájlok által elérhető](azure-netapp-files-network-topologies.md)olvasási és írási műveleteket az Active Directory tartományi szolgáltatások (ADDS) tartományvezérlőinek. Azt is megakadályozza, hogy a szolgáltatás olyan tartományvezérlőkkel kommunikáljon, amelyek nem szerepelnek a megadott Active Directory – helyek és szolgáltatások helyen. 

Ha az ADDS használatakor meg szeretné találni a webhely nevét, lépjen kapcsolatba a szervezet Active Directory tartományi szolgáltatásokért felelős felügyeleti csoportjával. Az alábbi példa az Active Directory – helyek és szolgáltatások beépülő modult mutatja be, ahol a webhely neve megjelenik: 

![Active Directory - helyek és szolgáltatások](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-and-services.png)

Amikor AD-kapcsolatot konfigurál az Azure NetApp-fájlokhoz, megadhatja a webhely nevét az **AD helynév** mező hatókörében.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory tartományi szolgáltatások 

Az Azure Active Directory tartományi szolgáltatások (AADDS) konfigurációját és útmutatását lásd: [Azure AD tartományi szolgáltatások dokumentációja.](https://docs.microsoft.com/azure/active-directory-domain-services/)

Az Azure NetApp-fájlokra további AADDS-szempontok vonatkoznak: 

* Győződjön meg arról, hogy a virtuális hálózat vagy az alhálózat, ahol az AADDS telepítve van, ugyanabban az Azure-régióban található, mint az Azure NetApp Files központi telepítése.
* Ha egy másik virtuális hálózatot használ abban a régióban, ahol az Azure NetApp-fájlok telepítve van, hozzon létre egy társviszony-létesítést a két virtuális hálózat között.
* Az Azure NetApp `user` `resource forest` Files támogatja és írja.
* A szinkronizálás típusának kiválasztásához `All` válassza `Scoped`a vagy a lehetőséget.   
    Ha a `Scoped`lehetőséget választja, győződjön meg arról, hogy a megfelelő Azure AD-csoport van kiválasztva az SMB-megosztások eléréséhez.  Ha bizonytalan, használhatja a `All` szinkronizálás típusát.
* A nagyvállalati vagy prémium termékváltozat használata szükséges. A standard termékváltozat nem támogatott.

Active Directory-kapcsolat létrehozásakor vegye figyelembe az AADDS következő sajátosságait:

* **Az Elsődleges DNS**, **másodlagos DNS**és **AD DNS-tartománynév** az AADDS menüben található információ.  
DNS-kiszolgálók esetén két IP-cím lesz használva az Active Directory-kapcsolat konfigurálására. 
* A szervezeti egység `OU=AADDC Computers`elérési **útja** .  
Ez a beállítás a **NetApp-fiók** **Active Directory-kapcsolatok** csoportban van konfigurálva:

  ![Szervezeti egység elérési útja](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **A felhasználónév** hitelesítő adatai bármely felhasználó lehetnek, aki az Azure AD csoport **Azure AD DC-rendszergazdáknak**a tagja.


## <a name="create-an-active-directory-connection"></a>Active Directory-kapcsolat létrehozása

1. NetApp-fiókjában kattintson az **Active Directory-kapcsolatok**elemre, majd a **Csatlakozás gombra.**  

    ![Active Directory-kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Az Active Directoryhoz való csatlakozás ablakban adja meg a következő információkat a használni kívánt tartományi szolgáltatások alapján:  

    A használt tartományi szolgáltatásokkal kapcsolatos információkért [lásd: A használni kívánt tartományi szolgáltatások eldöntése](#decide-which-domain-services-to-use). 

    * **Elsődleges DNS**  
        Ez az Active Directory tartományhoz való csatlakozáshoz és az SMB hitelesítési műveletekhez szükséges DNS. 
    * **Másodlagos DNS**   
        Ez a másodlagos DNS-kiszolgáló a redundáns névszolgáltatások biztosítására. 
    * **AD DNS-tartományneve**  
        Ez annak az Active Directory tartományi szolgáltatásnak a tartományneve, amelyhez csatlakozni szeretne.
    * **AD-webhely neve**  
        Ez az a helynév, amelyre a tartományvezérlő felderítése korlátozott lesz.
    * **SMB-kiszolgáló (számítógépfiók) előtagja**  
        Ez az Active Directory ban lévő számítógépfiók elnevezési előtagja, amelyet az Azure NetApp Files új fiókok létrehozásához fog használni.

        Ha például a szervezet által a fájlkiszolgálókhoz használt elnevezési szabvány NAS-01, NAS-02..., NAS-045, akkor az előtaghoz "NAS" értéket kell megadnia. 

        A szolgáltatás szükség szerint további számítógépfiókokat hoz létre az Active Directoryban.

    * **Szervezeti egység elérési útja**  
        Ez annak a szervezeti egységnek az LDAP-elérési útja, ahol az SMB-kiszolgálószámítógép-fiókok létrejönnek. Ez azt, OU = második szint, OU = első szinten. 

        Ha Az Azure NetApp-fájlokat az Azure Active Directory tartományi `OU=AADDC Computers` szolgáltatásokkal használja, a szervezeti egység elérési útja az Active Directory konfigurálása a NetApp-fiókhoz.
        
    * Hitelesítő adatok, beleértve a **felhasználónevet** és **a jelszót**

    ![Csatlakozás az Active Directoryhoz](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Kattintson a **Csatlakozás** parancsra.  

    Megjelenik a létrehozott Active Directory-kapcsolat.

    ![Active Directory-kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Az Active Directory-kapcsolat mentése után szerkesztheti a felhasználónév- és jelszómezőket. A kapcsolat mentése után más értékek nem szerkeszthetők. Ha más értékeket is módosítania kell, először törölnie kell az üzembe helyezett SMB-köteteket, majd törölnie kell, majd újra létre kell hoznia az Active Directory-kapcsolatot.

## <a name="add-an-smb-volume"></a>SMB-kötet hozzáadása

1. Kattintson a Kapacitáskészletek panel **Kötetek** paneljének elemre. 

    ![Navigálás a kötetekre](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Megjelenik a Kötet létrehozása ablak.

3. A Kötet létrehozása ablakban kattintson a **Létrehozás** és a következő mezők adatainak megadására:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötetnevének minden kapacitáskészleten belül egyedinek kell lennie. Legalább három karakter hosszúnak kell lennie. Bármilyen alfanumerikus karaktert használhat.   

        Kötetnévként nem `default` használható.

    * **Kapacitáskészlet**  
        Adja meg azt a kapacitáskészletet, amelyhez a kötetet létre kívánja hozni.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Adja meg azt az Azure virtuális hálózatot (VNet), amelyből a kötetet el szeretné érni.  

        A megadott virtuális hálózatnak rendelkeznie kell az Azure NetApp-fájlok delegált alhálózatával. Az Azure NetApp-fájlok szolgáltatás csak ugyanabból a virtuális hálózatból vagy egy virtuális hálózatról érhető el, amely ugyanabban a régióban van, mint a kötet a virtuális hálózat társviszony-létesítésén keresztül. A kötetet a helyszíni hálózatról is elérheti az Expressz útvonalon keresztül.   

    * **Alhálózat**  
        Adja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell az Azure NetApp-fájlokban. 
        
        Ha még nem delegált alhálózatot, kattintson az **Új létrehozása** gombra a Kötet létrehozása lapon. Ezután az Alhálózat létrehozása lapon adja meg az alhálózati adatokat, és válassza a **Microsoft.NetApp/kötetek** lehetőséget az Azure NetApp-fájlok alhálózatának delegálásához. Minden virtuális hálózatban csak egy alhálózat delegálható az Azure NetApp-fájlokszámára.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kattintson a **Protokoll** elemre, és töltse ki a következő információkat:  
    * Válassza az **SMB** protokolltípust a kötethez. 
    * Válassza ki az **Active Directory-kapcsolatot** a legördülő listából.
    * Adja meg a megosztott kötet nevét a **Megosztás név ben.**

    ![SMB protokoll megadása](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kattintson **a Véleményezés + Létrehozás gombra** a kötet részleteinek áttekintéséhez.  Ezután kattintson a **Létrehozás** gombra az SMB-kötet létrehozásához.

    A létrehozott kötet megjelenik a Kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="next-steps"></a>További lépések  

* [Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [SMB – gyakori kérdések](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [További információ az Azure-szolgáltatások virtuális hálózati integrációjáról](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Új Active Directory-erdő telepítése az Azure CLI használatával](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
