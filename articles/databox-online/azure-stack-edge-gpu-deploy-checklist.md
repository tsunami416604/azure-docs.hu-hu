---
title: Előtelepítési ellenőrzőlista Azure Stack Edge-eszköz üzembe helyezéséhez | Microsoft Docs
description: Ez a cikk az Azure Stack Edge-eszköz üzembe helyezése előtt összegyűjthető információkat ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/26/2020
ms.author: alkohli
ms.openlocfilehash: 065a6595ba89e00310ff41cd63b929f6d6d83222
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084466"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-device"></a>Üzembe helyezési ellenőrzőlista az Azure Stack Edge-eszközhöz  

Ez a cikk azokat az információkat ismerteti, amelyeket az Azure Stack Edge-eszköz tényleges üzembe helyezése előtt össze lehet gyűjteni. 

Az alábbi ellenőrzőlista segítségével gondoskodhat arról, hogy a Azure Stack Edge-eszközre vonatkozó megrendelés után és az eszköz megérkezése előtt megtekintse ezeket az információkat. 

## <a name="deployment-checklist"></a>Üzembe helyezési ellenőrzőlista 

| Fázis                             | Paraméter                                                                                                                                                                                                                           | Részletek                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Eszközkezelés               | <li>Előfizetés Microsoft Azure</li><li>Azure Active Directory – Graph API</li><li>Microsoft Azure Storage fiók</li>|<li>Engedélyezve Azure Stack Edge/Data Box Gateway, közreműködői engedélyek</li><li>Rendszergazdai vagy felhasználói hozzáférés biztosítása</li><li>Hozzáférési hitelesítő adatok szükségesek</li> |
| Eszköztelepítés               | A csomagban lévő hálózati kábelek. <br>NEKÜNK egy SVE 18/3-es, 125 V és 15 amper értékkel rendelkező 15P, amely egy néma 5 – C13 (kimeneti bemenet) összekötőt tartalmaz.                                                                                                                                                                                                          | Az eszközzel van ellátva.<br>További információkért tekintse meg az [ország által támogatott Power vezetékek](azure-stack-edge-technical-specifications-power-cords-regional.md) listáját.                                                                                        |
|                                   | <li>Legalább 1 X 1 – GbE RJ-45 hálózati kábel az 1. porthoz </li><li> Legalább 1 X 25 – GbE SFP + réz kábel a 3-as porthoz, 4. port, 5. port vagy 6. port</li>| Az ügyfélnek be kell szereznie ezeket a kábeleket.<br>A támogatott hálózati kábelek, kapcsolók és adóvevők teljes listájáért lásd: [Cavium FastlinQ 41000 sorozat együttműködési mátrixa.](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)                                                                          | 
| Eszköz csatlakoztatása első alkalommal      | Az 1. port rögzített IP-címmel (192.168.100.10/24) rendelkezik a kezdeti kapcsolathoz. <li>Az 1. porthoz való közvetlen csatlakozáshoz laptopra van szükség a 192.168.100.0/24 hálózat IP-címével.</li><li> Az eszköz helyi felhasználói felületének használata a `https://192.168.100.10` következő helyen: további konfigurálás.</li>|                                                                                                                   |
| Eszköz bejelentkezése                      | Az eszköz rendszergazdai jelszavának 8 és 16 karakter közöttinek kell lennie. <br>A következő karakterek közül hármat tartalmaznia kell: nagybetűk, kisbetűk, számok és speciális karakterek.                                            | Az alapértelmezett jelszó a *jelszó1* , amely az első bejelentkezéskor lejár.                                                     |
| Hálózati beállítások                  | Az eszköz 2 x 1 GbE, 4 x 25 GbE hálózati portot tartalmaz. <li>Az 1. port csak a felügyeleti beállítások konfigurálására szolgál. Egy vagy több adatport csatlakozhat és konfigurálható. </li><li> Legalább egy adathálózati adaptert a 2. port közül kell csatlakoztatni az internethez (az Azure-hoz való csatlakozással).</li><li> Az IP-beállítások támogatják a DHCP/statikus IPv4-konfigurációt. | A statikus IPv4-konfigurációhoz az IP, a DNS-kiszolgáló és az alapértelmezett átjáró szükséges.                                                                                                                  |
| Számítási hálózati beállítások     | <li>2 statikus nyilvános IP-cím szükséges a Kubernetes-csomópontokhoz, és legalább egy statikus IP-címet Azure Stack Edge hub szolgáltatáshoz a számítási modulok eléréséhez.</li><li>Minden olyan extra szolgáltatáshoz vagy tárolóhoz egy IP-címet kell megkövetelni, amelyet a Kubernetes-fürtön kívülről kell elérni.</li>                                                                                                                       | Csak a statikus IPv4-konfiguráció támogatott.                                                                      |
| Választható Webproxy beállításai     | <li>Webproxy kiszolgáló IP-címe/FQDN, Port </li><li>Webproxy felhasználóneve, jelszó</li>                                                                                                                                                                                                    | Jelenleg nem támogatott a számítási beállítás.                                                                     |
| Tűzfal-és portbeállítások        | Az eszközök IP [-címei számára a felsorolt URL-minták és portok](azure-stack-edge-system-requirements.md#networking-port-requirements) használata engedélyezett.                                                                                                                                                  |                                                                                                                   |
| Választható MAC-címe            | Ha a MAC-címnek engedélyezési listára van szüksége, kérje le a csatlakoztatott port címeit az eszköz helyi felhasználói felületéről. |                                                                                                                   |
| Választható Hálózati kapcsoló portja    | Az eszköz Hyper-V virtuális gépeket üzemeltet a számítási feladatokhoz. Egyes hálózati kapcsolók portjai nem rendelkeznek alapértelmezett beállításokkal.                                                                                                        |                                                                                                                   |
| Ajánlott Idő beállításai       | Konfigurálja az időzónát, az elsődleges NTP-kiszolgálót, a másodlagos NTP-kiszolgálót.                                                                                                                                                                    | Konfigurálja az elsődleges és a másodlagos NTP-kiszolgálót a helyi hálózaton.<br>Ha a helyi kiszolgáló nem érhető el, a nyilvános NTP-kiszolgálókat is konfigurálhatja.                                                    |
| Választható Frissítési kiszolgáló beállításai | <li>A frissítési kiszolgáló IP-címének megkövetelése a helyi hálózaton, a WSUS-kiszolgáló elérési útja. </li> | Alapértelmezés szerint a rendszer a nyilvános Windows Update Servert használja.|
| Eszközbeállítások                   | <li>A DNS-szervezeten belül regisztrált eszköz neve </li><li>DNS-tartomány</li> |                                                                                                                   |
| Választható Tanúsítványok                      | Az eszköz által generált tanúsítványok használata <br><br> Ha saját tanúsítványokat hoz üzembe, a következőkre lesz szüksége: <li>Megbízható legfelső szintű aláíró tanúsítvány DER formátumban a *. cer* formátummal </li><li>Végponti tanúsítványok *pfx* formátumban</li>|A végponti tanúsítványok a következők: Azure Resource Manager, blob Storage, helyi webes felhasználói felület.                                                                                                                   |
| Aktiválás                        | Aktiválási kulcs megkövetelése az Azure Stack Edge/Data Box Gateway erőforrásból.                                                                                                                                                       | A generált kulcs 3 nap múlva lejár.                                                                        |


## <a name="next-steps"></a>Következő lépések

Felkészülés az [Azure stack Edge-eszköz](azure-stack-edge-gpu-deploy-prep.md)üzembe helyezésére.



