---
title: Az Azure Site Recovery hibaelhárítása az Azure – Azure replikációval kapcsolatos problémákat és hibákat |} A Microsoft Docs
description: Vész-helyreállítási Azure-beli virtuális gépek replikálása hibáinak és problémáinak elhárítása
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sujayt
ms.openlocfilehash: 7f821c86712b239435d01cfc377da15c498fe7bf
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219872"
---
# <a name="troubleshoot-azure-to-azure-vm-replication-issues"></a>Azure – Azure virtuális gép replikálási problémák elhárítása

Ez a cikk az Azure Site Recoveryben replikálása és helyreállítása Azure-beli virtuális gépek egyik régióból egy másik régióba gyakori problémákat ismerteti, és azok megoldását ismerteti. Támogatott konfigurációk kapcsolatos további információkért lásd: a [támogatási mátrixa Azure virtuális gépek replikálása](site-recovery-support-matrix-azure-to-azure.md).

## <a name="list-of-errors"></a>Hibák listája
- **[Azure-erőforrás kvótával kapcsolatos problémák (hibakód: 150097)](#azure-resource-quota-issues-error-code-150097)** 
- **[Megbízható legfelső szintű tanúsítványok (hibakód: 151066)](#trusted-root-certificates-error-code-151066)** 
- **[Kimenő kapcsolat esetén a Site Recovery (hibakód: 151195)](#issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br)** 

## <a name="azure-resource-quota-issues-error-code-150097"></a>Azure-erőforrás kvótával kapcsolatos problémák (hibakód: 150097)
Az előfizetés engedélyezni kell a célrégióban a vészhelyreállítási régióban használni kívánt Azure-beli virtuális gépek létrehozásához. Emellett az előfizetés meghatározott méretű virtuális gépek létrehozásához engedélyezett kvóta elegendő kell rendelkeznie. Alapértelmezés szerint a Site Recovery a forrásoldali virtuális Géppel megegyező méretű a cél virtuális gép választja ki. Ha a megfelelő méret nem érhető el, a legközelebbi lehetséges mérete automatikusan követi. Ha nincs megfelelő mérete, amely támogatja a forrás virtuális gép konfigurációs van, ez a hibaüzenet jelenik meg:

**Hibakód:** | **Lehetséges okok** | **Az ajánlás**
--- | --- | ---
150097<br></br>**Üzenet**: Replikáció nem sikerült engedélyezni a virtuális gép VmName. | – Az előfizetés-azonosító nem engedélyezhetők olyan virtuális gépek létrehozása a célhelyen régióban.</br></br>– Az előfizetési azonosító nincs engedélyezve, vagy nem rendelkezik elegendő kvótával a célhelyen régió adott Virtuálisgép-méretek létrehozására.</br></br>-A megfelelő cél virtuális gép méretét, amely megegyezik a forrás virtuális gép hálózati adapter száma (2) az előfizetés-azonosító nem található a célhelyen régióban.| Kapcsolattartó [Azure számlázási ügyfélszolgálatát](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) a szükséges Virtuálisgép-méretek a célhelyen az előfizetéshez tartozó virtuális gépek létrehozásának engedélyezéséhez. Miután engedélyezte, próbálkozzon újra a sikertelen műveletet.

### <a name="fix-the-problem"></a>A probléma megoldása
Felveheti a kapcsolatot [Azure számlázási ügyfélszolgálatát](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) hozhat létre a szükséges méretű virtuális gépeket a célhelyen az előfizetés engedélyezése érdekében.

Ha a célhelyen kapacitás korlátozást tartalmaz, tiltsa le a replikációt, és engedélyezi azt egy másik helyre, ahol az előfizetése elegendő kvótával rendelkezik a szükséges méretű virtuális gépek létrehozásához.

## <a name="trusted-root-certificates-error-code-151066"></a>Megbízható legfelső szintű tanúsítványok (hibakód: 151066)

Ha a legújabb megbízható főtanúsítványok nem találhatók megtalálható a virtuális Gépen, a "replikáció engedélyezése" feladat meghiúsulhat. A tanúsítványok nélkül Ha hitelesítését és engedélyezését a virtuális gép Site Recovery szolgáltatás hívás sikertelen. A sikertelen "replikáció engedélyezése" Site Recovery feladat a hibaüzenet jelenik meg:

**Hibakód:** | **Lehetséges ok** | **Javaslatok**
--- | --- | ---
151066<br></br>**Üzenet**: Nem sikerült konfigurálni a Site Recoveryt. | A szükséges megbízható főtanúsítványok engedélyezési és a hitelesítés nem található a gépen. | – A Windows operációs rendszert futtató virtuális gép, győződjön meg arról, hogy a megbízható főtanúsítványok megtalálhatók-e a gépen. További információ: [konfigurálása a megbízható főtanúsítványok és a nem engedélyezett tanúsítványok](https://technet.microsoft.com/library/dn265983.aspx).<br></br>– A Linux operációs rendszert futtató virtuális gép esetén kövesse a Linux operációs rendszer verzió terjesztője által közzétett megbízható főtanúsítványok útmutatóját.

### <a name="fix-the-problem"></a>A probléma megoldása
**Windows**

A legújabb Windows frissítések telepítése a virtuális gépen, hogy az összes megbízható legfelső szintű tanúsítványok találhatók a gépen. Ha Ön leválasztott környezetben, hajtsa végre a szabványos Windows frissítési folyamatot a szervezet tanúsítványok beolvasása. Ha a szükséges tanúsítványok nem megtalálható a virtuális Gépen, a Site Recovery szolgáltatás felé irányuló biztonsági okokból nem sikerült.

Kövesse a szokásos Windows update management vagy a tanúsítvány frissítéskezelési folyamat a szervezetben a legújabb főtanúsítványok és a frissített visszavont tanúsítványok listájának lekérése a virtuális gépeken.

Győződjön meg arról, hogy a probléma megoldódott, lépjen a virtuális gépen egy böngészőből login.microsoftonline.com.

**Linux**

Kövesse az útmutatást, a Linux-terjesztőn úgy szerezheti be a legújabb megbízható főtanúsítványok és a legfrissebb visszavonttanúsítvány-listát a virtuális gép által biztosított.

Mivel a SuSE Linux symlinks tanúsítvány listának a karbantartására használ, kövesse az alábbi lépéseket:

1.  Jelentkezzen be gyökérszintű felhasználóként.

2.  Módosítsa a könyvtárat a következő parancs futtatásával.

      ``# cd /etc/ssl/certs``

1. Ellenőrizze, hogy ha a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány telepítve lett-e.

      ``# ls VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

2. Ha a Symantec legfelső szintű Hitelesítésszolgáltatói tanúsítvány nem található, a következő parancsot a fájl letöltéséhez. Keressen hibákat, és hajtsa végre a javasolt művelet hálózati hibák.

      ``# wget https://www.symantec.com/content/dam/symantec/docs/other-resources/verisign-class-3-public-primary-certification-authority-g5-en.pem -O VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

3. Ellenőrizze, hogy a Baltimore legfelső szintű Hitelesítésszolgáltatói tanúsítvány megtalálható.

      ``# ls Baltimore_CyberTrust_Root.pem``

4. Ha a Baltimore legfelső szintű Hitelesítésszolgáltatói tanúsítvány nem található, töltse le a tanúsítványt.  

    ``# wget http://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem -O Baltimore_CyberTrust_Root.pem``

5. Ellenőrizze, hogy ha a DigiCert_Global_Root_CA tanúsítvány telepítve lett-e.

    ``# ls DigiCert_Global_Root_CA.pem``

6. Ha a DigiCert_Global_Root_CA nem található, a következő parancsokat a tanúsítvány letöltése.

    ``# wget http://www.digicert.com/CACerts/DigiCertGlobalRootCA.crt``

    ``# openssl x509 -in DigiCertGlobalRootCA.crt -inform der -outform pem -out DigiCert_Global_Root_CA.pem``

7. A tanúsítvány frissítése rehash parancsprogram futtatásával az újonnan letöltött tanúsítványok a tulajdonos kivonatokat.

    ``# c_rehash``

8.  Ellenőrizze, hogy ha a tulajdonos kivonatolja a tanúsítványok symlinks létrehozásakor.

    - Parancs

      ``# ls -l | grep Baltimore``

    - Kimenet

      ``lrwxrwxrwx 1 root root   29 Jan  8 09:48 3ad48a91.0 -> Baltimore_CyberTrust_Root.pem
      -rw-r--r-- 1 root root 1303 Jun  5  2014 Baltimore_CyberTrust_Root.pem``

    - Parancs

      ``# ls -l | grep VeriSign_Class_3_Public_Primary_Certification_Authority_G5``

    - Kimenet

      ``-rw-r--r-- 1 root root 1774 Jun  5  2014 VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem
      lrwxrwxrwx 1 root root   62 Jan  8 09:48 facacbc6.0 -> VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem``

    - Parancs

      ``# ls -l | grep DigiCert_Global_Root``

    - Kimenet

      ``lrwxrwxrwx 1 root root   27 Jan  8 09:48 399e7759.0 -> DigiCert_Global_Root_CA.pem
      -rw-r--r-- 1 root root 1380 Jun  5  2014 DigiCert_Global_Root_CA.pem``

9.  Hozzon létre egy másolatot a fájlról VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem filename b204d74a.0

    ``# cp VeriSign_Class_3_Public_Primary_Certification_Authority_G5.pem b204d74a.0``

10. Hozzon létre egy másolatot a fájlról Baltimore_CyberTrust_Root.pem filename 653b494a.0

    ``# cp Baltimore_CyberTrust_Root.pem 653b494a.0``

13. Hozzon létre egy másolatot a fájlról DigiCert_Global_Root_CA.pem filename 3513523f.0

    ``# cp DigiCert_Global_Root_CA.pem 3513523f.0``  


14. Ellenőrizze, hogy megtalálhatók-e a fájlokat.  

    - Parancs

      ``# ls -l 653b494a.0 b204d74a.0 3513523f.0``

    - Kimenet

      ``-rw-r--r-- 1 root root 1774 Jan  8 09:52 3513523f.0
      -rw-r--r-- 1 root root 1303 Jan  8 09:52 653b494a.0
      -rw-r--r-- 1 root root 1774 Jan  8 09:52 b204d74a.0``


## <a name="outbound-connectivity-for-site-recovery-urls-or-ip-ranges-error-code-151037-or-151072"></a>A Site Recovery URL-címek vagy IP-címtartományokat (hibakód: 151037 vagy 151072) kimenő kapcsolatok

A Site Recovery replikációja, a kimenő kapcsolat az adott URL-címek vagy IP-címtartományok szükség a virtuális gépről. Ha a virtuális gép tűzfal mögött található, vagy használja a hálózati biztonsági csoport (NSG) szabályai kimenő kapcsolat szabályozásához, előfordulhat, hogy között ezek a problémák egyike.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>1. hiba: Nem sikerült regisztrálni az Azure virtuális gépet a Site Recovery (151195) </br>
- **Lehetséges ok** </br>
  - A site recovery végpontok DNS-feloldási hiba miatt nem létesíthető kapcsolat.
  - Ez gyakran látható ismételt védelem során a virtuális gép feladatátadása, de a DNS-kiszolgáló nem érhető el a DR régióban.
  
- **Felbontás**
   - Ha egyéni DNS használ, akkor ügyeljen arra, hogy a DNS-kiszolgáló érhető el a vész-helyreállítási régióban. Ha egy egyéni DNS nyissa meg a virtuális gép rendelkezik-e > vész-helyreállítási hálózat > DNS-kiszolgálók. Próbálja ki a virtuális gépről a DNS-kiszolgáló eléréséhez. Ha nem érhető el ezt követően testre is elérhető-e a DNS-kiszolgáló feladatátvétele vagy a sor közötti DR hálózati és a DNS-hely létrehozása.
  
    ![COM-hiba](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)
 

### <a name="issue-2-site-recovery-configuration-failed-151196"></a>2. hiba: Konfigurálni a Site Recovery nem tudta (151196)
- **Lehetséges ok** </br>
  - Az Office 365 portál és identitás IP4 végpontok nem létesíthető kapcsolat.

- **Felbontás**
  - Az Azure Site Recovery-hitelesítéshez szükséges hozzáférés az Office 365 IP-címek tartományát.
    Ha az Azure hálózati biztonsági csoport (NSG) szabályai, illetve a tűzfal proxy segítségével szabályozza a kimenő hálózati kapcsolatokra a virtuális Gépen, győződjön meg arról, Office 365 IP-tartományokkal való kommunikáció engedélyezése. Hozzon létre egy [Azure Active Directory (AAD) szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) alapú Hálózatibiztonságicsoport-szabály engedélyezi a hozzáférést az aad-hez tartozó összes IP-címek számára
        - Hozzáadja az új címeket az Azure Active Directory (AAD) a jövőben, ha szeretne létrehozni új NSG-szabályokat.


### <a name="issue-3-site-recovery-configuration-failed-151197"></a>3. hiba: Konfigurálni a Site Recovery nem tudta (151197)
- **Lehetséges ok** </br>
  - Nem lehet kapcsolatot az Azure Site Recovery szolgáltatási végpontjait.

- **Felbontás**
  - Az Azure Site Recovery szükséges hozzáférést [Site Recovery IP-címtartományok](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) a régiójától függően. Győződjön meg arról, hogy a szükséges ip-címtartományok érhetők el a virtuális gép.
    

### <a name="issue-4-a2a-replication-failed-when-the-network-traffic-goes-through-on-premise-proxy-server-151072"></a>4. hiba: A2A-replikáció sikertelen volt, amikor a hálózati forgalom halad át a helyi proxykiszolgáló (151072)
 - **Lehetséges ok** </br>
   - Érvénytelenek az egyéni proxy beállításait, és az ASR a mobilitási szolgáltatás ügynökének fejeződött nincs automatikus észlelés a proxybeállításokat az Internet Explorer


 - **Felbontás**
   1.   A mobilitási szolgáltatás ügynökének a proxybeállításokat az Internet Explorer a Windows és Linux rendszeren /etc/environment észleli.
   2.  Ha inkább a állítsa be a proxy csak az automatikus rendszer-Helyreállítás a mobilitási szolgáltatást, majd megadhatja a proxy adatait a ProxyInfo.conf helyen található:</br>
       - ``/usr/local/InMage/config/`` a ***Linux***
       - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` a ***Windows***
   3.   A ProxyInfo.conf kell a proxybeállításokat a következő INI-formátumban.</br>
                   *[proxy]*</br>
                   *Cím =http://1.2.3.4*</br>
                   *Port = 567*</br>
   4. Csak támogatja az automatikus rendszer-Helyreállítás a mobilitási szolgáltatás ügynökének ***nem hitelesített proxyk***.
 

### <a name="fix-the-problem"></a>A probléma megoldása
Az engedélyezési listára [a szükséges URL-címek](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) vagy a [szükséges IP-címtartományok](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), kövesse a [hálózati dokumentum](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="disk-not-found-in-the-machine-error-code-150039"></a>A lemez nem található a gépen (hibakód: 150039)

Egy új lemezt a virtuális Géphez csatolt inicializálni kell.

**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
150039<br></br>**Üzenet**: Az Azure data lemezt (DiskName) (DiskURI) rendelkező logikai egységen (LUN) (LUNValue) nem egy megfelelő lemezt a virtuális gép, amely azonos LUN-értékkel rendelkezik által jelentett megfeleltetni. | – Egy új adatlemezt a virtuális géphez lett csatolva, de azt nem lett inicializálva.</br></br>-Az adatlemezt a virtuális gép megfelelően nem jelent meg a LUN-értékkel, amelyen a lemez a virtuális géphez lett csatolva.| Győződjön meg arról, hogy az adatlemezek inicializálása, és próbálkozzon újra a művelettel.</br></br>Windows esetén: [Csatolja, és a egy új lemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).</br></br>Linux esetén: [A Linux új adatlemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

### <a name="fix-the-problem"></a>A probléma megoldása
Győződjön meg arról, hogy az adatlemezek inicializálása megtörtént, és próbálkozzon újra a művelettel:

- Windows esetén: [Csatolja, és a egy új lemez inicializálása](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal).
- Linux esetén: [lemez hozzáadása egy új data Linux](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk).

Ha a probléma tartósan fennáll, forduljon az ügyfélszolgálathoz.


## <a name="unable-to-see-the-azure-vm-for-selection-in-enable-replication"></a>Nem látja az Azure virtuális gép kiválasztása a "replikáció engedélyezése"

 **1. ok:  Erőforráscsoport és a forrás virtuális gép máshol helyezkedik el** <br>
Az Azure Site Recovery jelenleg meghatalmazott, hogy régió erőforráscsoportot és a virtuális gépek ugyanazon a helyen legyen. Ha ez nem így majd akkor nem tudná az védelem alatt a virtuális gép található.

**2. ok: Erőforráscsoport nem része a kiválasztott előfizetéshez** <br>
Meg nem találja az erőforráscsoport a védelem idején, ha nem az adott előfizetés részeként. Győződjön meg arról, hogy az erőforráscsoport tartozik-e az előfizetés, amely használatban van.

 **3. ok: Elavult konfiguráció** <br>
Ha nem látja a virtuális gép replikációs engedélyezni szeretné, akkor előfordulhat, hogy egy elavult a Site Recovery-konfiguráció miatt maradhat az Azure virtuális gépen. Az elavult konfiguráció maradhat egy Azure virtuális gépen a következő esetekben:

- Engedélyezve van a replikáció az Azure virtuális gép Site Recovery használatával és anélkül, hogy explicit módon letiltja a virtuális gép replikációs törli a Site Recovery-tárból.
- Engedélyezve van a replikáció az Azure virtuális gép Site Recovery használatával, és törölt anélkül, hogy explicit módon a replikáció a virtuális gép letiltása a Site Recovery-tároló tartalmazó erőforráscsoportot.

### <a name="fix-the-problem"></a>A probléma megoldása

>[!NOTE] 
>
>Ne feledje frissíteni a "" AzureRM.Resources"" modul használata előtt az alábbi szkriptet. 

Használhat [távolítsa el az elavult automatikus konfigurációs szkript](https://gallery.technet.microsoft.com/Azure-Recovery-ASR-script-3a93f412) , és távolítsa el az elavult a Site Recovery konfigurálása az Azure virtuális gépen. A virtuális gép láthatja a elavult konfiguráció eltávolítása után kell lennie.

## <a name="unable-to-select-virtual-machine-for-protection"></a>Válassza ki a virtuális gép védelemre nem sikerült 
 **1. ok:  Virtuális gép rendelkezik néhány bővítmény telepítve állapota sikertelen, vagy nem válaszol** <br>
 Lépjen a virtuális gépek > Beállítás > bővítmények és annak ellenőrzése, hogy minden olyan bővítmények hibás állapotban vannak. Távolítsa el a sikertelen bővítményt, és próbálkozzon újra a virtuális gép védelmét.<br>
 **2. ok:  [Virtuális gép kiépítési állapota nem érvényes](#vms-provisioning-state-is-not-valid-error-code-150019)**

## <a name="vms-provisioning-state-is-not-valid-error-code-150019"></a>Virtuális gép kiépítési állapota nem érvényes (hibakód: 150019)

Ahhoz, hogy a gép replikációját, a kiépítési állapotot kell **sikeres**. Az alábbi lépéseket követve ellenőrizheti a virtuális gép állapota.

1.  Válassza ki a **erőforrás-kezelő** a **minden szolgáltatás** az Azure Portalon.
2.  Bontsa ki a **előfizetések** listában, és válassza ki az előfizetését.
3.  Bontsa ki a **ResourceGroups** listában, és válassza ki az erőforráscsoportot a virtuális gép.
4.  Bontsa ki a **erőforrások** listájára és válassza ki a virtuális gép
5.  Ellenőrizze a **provisioningState** példányait tartalmazó nézet jobb oldali mezőbe.

### <a name="fix-the-problem"></a>A probléma megoldása

- Ha **provisioningState** van **sikertelen**, forduljon az ügyfélszolgálathoz hibaelhárítása adatokkal.
- Ha **provisioningState** van **Updating**, egy másik bővítmény sikerült első üzembe helyezhető. Annak ellenőrzése, hogy minden folyamatban lévő műveletek a virtuális Gépen, várja meg, hogy végezze el, és próbálkozzon újra a sikertelen Site Recovery **engedélyezze a replikációt** feladat.

## <a name="unable-to-select-target-virtual-network---network-selection-tab-is-grayed-out"></a>Nem sikerült jelölje ki a cél virtuális hálózat - hálózat kiválasztása lapon szürkén jelenik meg.

**1. ok: Ha a virtuális gép csatlakoztatva van egy hálózatot, amely a cél hálózati már hozzá van rendelve.**
- Ha a forrásoldali virtuális gép virtuális hálózat része, és a egy másik virtuális gépről az azonos virtuális hálózatban már le van képezve egy hálózati célerőforrás-csoportban, majd a alapértelmezett hálózat kiválasztása legördülő lista letiltásra kerül.

![Network_Selection_greyed_out](./media/site-recovery-azure-to-azure-troubleshoot/unabletoselectnw.png)

**2. ok: Ha korábban a virtuális gép az Azure Site Recovery által védett, és le van tiltva a replikáció.**
 - A virtuális gépek replikáció letiltása nem törli a Hálózatleképezés. A recovery Services-tároló, amelyben a virtuális gép védett ből törlendő rendelkezik. </br>
 Lépjen a recovery Services-tároló > Site Recovery-infrastruktúra > hálózatleképezés. </br>
 ![Delete_NW_Mapping](./media/site-recovery-azure-to-azure-troubleshoot/delete_nw_mapping.png)
 - A vész-helyreállítási telepítése során konfigurált célhálózat után a kezdeti beállítása, a virtuális gép védelme után is módosítható. </br>
 ![Modify_NW_mapping](./media/site-recovery-azure-to-azure-troubleshoot/modify_nw_mapping.png)
 - Vegye figyelembe, hogy a hálózatleképezés módosítása hatással van az összes, adott hálózatleképezés használó virtuális gépek nem védett.


## <a name="comvolume-shadow-copy-service-error-error-code-151025"></a>COM +/ hiba a kötet árnyékmásolata szolgáltatás (hibakód: 151025)
**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
151025<br></br>**Üzenet**: Nem sikerült telepíteni a Site recovery-bővítmény | – "A COM + System Application" szolgáltatás le van tiltva.</br></br>– "A kötet árnyékmásolata" szolgáltatás le van tiltva.| Állítsa a "COM + System Application" és "Kötet árnyékmásolata" szolgáltatásokat, automatikus vagy kézi indításúra.

### <a name="fix-the-problem"></a>A probléma megoldása

Nyissa meg a "Szolgáltatások" konzolt, és győződjön meg, hogy a "COM + System Application" és "Kötet árnyékmásolata" nincsenek megadva "Letiltva" "Indítási típus".
  ![COM-hiba](./media/azure-to-azure-troubleshoot-errors/com-error.png)

## <a name="unsupported-managed-disk-size-error-code-150172"></a>Nem támogatott felügyelt lemez mérete (hibakód: 150172)


**Hibakód:** | **Lehetséges okok** | **Javaslatok**
--- | --- | ---
150172<br></br>**Üzenet**: Nem sikerült engedélyezni a védelmet, az a virtuális gép, mert (DiskName) méretű (DiskSize), amely kisebb, mint a minimális támogatott mérete 10 GB-ot. | – A lemez kisebb, mint 1024 MB-os támogatott méretet| Győződjön meg arról, hogy a lemezméretek a támogatott tartományon belül, és próbálja megismételni a műveletet. 

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-error-code-151126"></a>Nem sikerült, mert a GRUB-konfiguráció (hibakód: 151126) UUID helyett az említett eszköznév védelem engedélyezése

**Lehetséges ok:** </br>
A konfigurációs GRUB-fájlok ("/ boot/grub/menu.lst", "/ boot/grub/grub.cfg", "/ boot/grub2/grub.cfg" vagy "/ etc/alapértelmezett/grub") az értéket a paraméterek tartalmazhatja **legfelső szintű** és **folytatása** , a tényleges eszköznevek UUID helyett. A Site Recovery UUID megközelítés előírásoknak, módon eszközök név változhat között a virtuális gép újraindítása, a virtuális gép lehet, hogy nem érkeznek felfelé ugyanazzal a névvel feladatátvételi problémákat eredményez. Példa: </br>


- A következő sort a GRUB-fájlból áll **/boot/grub2/grub.cfg**. <br>
*Linux /boot/vmlinuz-3.12.49-11-default **= / dev/sda2 kiváltó** ${extra_cmdline} **= / dev/sda1 folytatása** beavatkozás nélküli, csendes showopts splash =*


- A következő sort a GRUB-fájlból áll **/boot/grub/menu.lst**
*kernel /boot/vmlinuz-3.0.101-63-default **= / dev/sda2 kiváltó** **= / dev/sda1 folytatása ** splash = beavatkozás nélküli crashkernel 256M-:128M showopts vga = = 0x314*

Ha a fenti félkövér karakterlánc megfigyelte, grub-HIBÁT nevét is tartalmazza, tényleges eszközre a paramétereket "root" és "Folytatás" UUID helyett.
 
**Hogyan háríthatja el:**<br>
Az eszköz nevét le kell cserélni a megfelelő UUID azonosítója.<br>


1. Az eszköz UUID található parancs végrehajtásával "blkid <device name>". Példa:<br>
```
blkid /dev/sda1 
```<br>
```/dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap" ```<br>
```blkid /dev/sda2```<br> 
```/dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
```<br>



1. Now replace the device name with its UUID in the format like "root=UUID=<UUID>". For example, if we replace the device names with UUID for root and resume parameter mentioned above in the files "/boot/grub2/grub.cfg", "/boot/grub2/grub.cfg" or "/etc/default/grub: then the lines in the files looks like. <br>
*kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
1. Restart the protection again


## Site recovery mobility service update completed with warnings ( error code 151083)
Site Recovery mobility service has many components, one of which is called filter driver. Filter driver gets loaded into system memory only at a time of system reboot. Whenever there are  site recovery mobility service updates that has filter driver changes, we update the machine but still gives you warning that some fixes require a reboot. It means that the filter driver fixes can only be realized when a new filter driver is loaded which can happen only at the time of system reboot.<br>
**Please note** that this is just a warning and existing replication keeps on working even after the new agent update. You can choose to reboot anytime you want to get the benefits of new filter driver but if you don't reboot than also old filter driver keeps on working. Apart from filter driver, **benefits of  any other enhancements and fixes in mobility service get realized without any reboot when the agent gets updated.**  

## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
