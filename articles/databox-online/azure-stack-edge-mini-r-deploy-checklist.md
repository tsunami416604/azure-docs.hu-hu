---
title: Előtelepítési ellenőrzőlista Azure Stack Edge mini R-eszköz üzembe helyezéséhez
description: Ez a cikk azokat az információkat ismerteti, amelyeket az Azure Stack Edge mini R-eszköz üzembe helyezése előtt össze lehet gyűjteni.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: c34240f55c091583cfed8c8fe966ae272d1fb453
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466893"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-mini-r-device"></a>Üzembe helyezési ellenőrzőlista az Azure Stack Edge mini R-eszközhöz  

Ez a cikk azokat az információkat ismerteti, amelyeket az Azure Stack Edge mini R-eszköz tényleges üzembe helyezése előtt össze lehet gyűjteni. 

Az alábbi ellenőrzőlista segítségével gondoskodhat róla, hogy a Azure Stack Edge mini R-eszközre vonatkozó megrendelés elvégzése után az eszköz megérkezése előtt meglegyenek az adatok. 

## <a name="deployment-checklist"></a>Üzembe helyezési ellenőrzőlista 

| Fázis                             | Paraméter                                                                                                                                                                                                                           | Részletek                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Eszközkezelés               | <li>Azure-előfizetés</li><li>Regisztrált erőforrás-szolgáltatók</li><li>Azure Storage-fiók</li>|<li>Engedélyezve van Azure Stack Edge mini R/Data Box Gateway, tulajdonosi vagy közreműködői hozzáféréshez.</li><li>A Azure Portalban nyissa meg a **Home >-Előfizetések > az előfizetéses > erőforrás-szolgáltatókat**. Keresse meg `Microsoft.DataBoxEdge` és regisztrálja. Ismételje meg a műveletet, `Microsoft.Devices` Ha IoT számítási feladatokat telepít.</li><li>Hozzáférési hitelesítő adatok szükségesek</li> |
| Eszköztelepítés               | A csomagban lévő hálózati kábelek. <br>NEKÜNK egy SVE 18/3-es, 125 V és 15 amper értékkel rendelkező 15P, amely egy néma 5 – C13 (kimeneti bemenet) összekötőt tartalmaz. | További információkért tekintse meg az [ország által támogatott Power vezetékek](azure-stack-edge-technical-specifications-power-cords-regional.md) listáját.  |
|                                   | <li>Legalább 1 X 1 – GbE RJ-45 hálózati kábel az 1. porthoz  </li><li> Legalább 1 X 25 – GbE SFP + réz kábel a 3-as porthoz, 4. port, 5. port vagy 6. port</li>| Az ügyfélnek be kell szereznie ezeket a kábeleket.<br>A támogatott hálózati kábelek, kapcsolók és adóvevők teljes listájáért lásd: [Cavium FastlinQ 41000 Series együttműködési mátrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) és [Mellanox Dual port 25G ConnectX-4 Channel hálózati adapter kompatibilis termékek](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Eszköz csatlakoztatása első alkalommal      | <li>A laptop, amelynek IPv4-beállításai módosíthatók. Ez a laptop kapcsolón vagy USB-n keresztül Ethernet-adapteren keresztül csatlakozik az 1-es porthoz.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Eszköz bejelentkezés                      | Az eszköz rendszergazdai jelszava 8 és 16 karakter között, és a következők közül hármat tartalmaz: nagybetűk, kisbetűk, számok és speciális karakterek.                                            | Az alapértelmezett jelszó a *jelszó1* , amely az első bejelentkezéskor lejár.                                                     |
| Hálózati beállítások                  | Az eszköz 2 x 1 GbE, 4 x 25 GbE hálózati portot tartalmaz. <li>Az 1. port csak a felügyeleti beállítások konfigurálására szolgál. Egy vagy több adatport csatlakozhat és konfigurálható. </li><li> Legalább egy adathálózati adaptert a 2. port közül kell csatlakoztatni az internethez (az Azure-hoz való csatlakozással).</li><li> A DHCP és a statikus IPv4-konfiguráció támogatott. | A statikus IPv4-konfigurációhoz az IP, a DNS-kiszolgáló és az alapértelmezett átjáró szükséges.   |
| Számítási hálózati beállítások     | <li>2 szabad, statikus, összefüggő IP-címeket igényel a Kubernetes-csomópontokhoz, és 1 statikus IP-címet IoT Edge szolgáltatáshoz.</li><li>Egy további IP-cím megkövetelése minden olyan extra szolgáltatáshoz vagy modulhoz, amelyet telepíteni fog.</li>| Csak a statikus IPv4-konfiguráció támogatott.|
| Választható Webproxy beállításai     | <li>Webproxy kiszolgáló IP-címe/FQDN, Port </li><li>Webproxy felhasználóneve, jelszó</li> | A webproxy nem támogatott a számítási beállítással. |
| Tűzfal-és portbeállítások        | Ha tűzfalat használ, győződjön meg arról, hogy a [felsorolt URL-minták és portok](azure-stack-edge-system-requirements.md#networking-port-requirements) engedélyezve vannak az eszközök IP-címei számára. |  |
| Ajánlott Idő beállításai       | Konfigurálja az időzónát, az elsődleges NTP-kiszolgálót, a másodlagos NTP-kiszolgálót. | Konfigurálja az elsődleges és a másodlagos NTP-kiszolgálót a helyi hálózaton.<br>Ha a helyi kiszolgáló nem érhető el, a nyilvános NTP-kiszolgálókat is konfigurálhatja.                                                    |
| Választható Frissítési kiszolgáló beállításai | <li>A frissítési kiszolgáló IP-címének megkövetelése a helyi hálózaton, a WSUS-kiszolgáló elérési útja. </li> | Alapértelmezés szerint a rendszer a nyilvános Windows Update Servert használja.|
| Eszközbeállítások | <li>Eszköz teljes tartományneve (FQDN) </li><li>DNS-tartomány</li> | |
| Választható Tanúsítványok  | A nem éles munkaterhelések teszteléséhez használja a [tanúsítványok előállítása lehetőséget](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) . <br><br> Ha saját tanúsítványokat hoz létre, beleértve az aláíró lánc (oka) t, [adja hozzá a tanúsítványokat](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) a megfelelő formátumhoz.| Csak akkor konfigurálja a tanúsítványokat, ha megváltoztatja az eszköz nevét és/vagy a DNS-tartományt. |
| Aktiválás  | Aktiválási kulcs megkövetelése az Azure Stack Edge-erőforrásból.    | A generált kulcs 3 nap múlva lejár. |

<!--
| (Optional) MAC Address            | If MAC address needs to be whitelisted, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>További lépések

Felkészülés az [Azure stack Edge mini R-eszköz](azure-stack-edge-gpu-deploy-prep.md)üzembe helyezésére.
