---
title: Az Azure HDInsight felügyeleti IP-címei
description: Ismerje meg, hogy mely IP-címeket kell engedélyeznie a bejövő forgalmat, hogy megfelelően konfigurálhassa a hálózati biztonsági csoportokat és a felhasználó által definiált útvonalakat az Azure HDInsight-mal való virtuális hálózatkezeléshez.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 5e8f39b58f258742108fe323d9395efd87bc288f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271812"
---
# <a name="hdinsight-management-ip-addresses"></a>HDInsight-kezelés IP-címei

> [!Important]
> A legtöbb esetben most már használhatja a [szolgáltatáscímke](hdinsight-service-tags.md) funkciót a hálózati biztonsági csoportokhoz, ahelyett, hogy manuálisan adná hozzá az IP-címeket. Az új régiók csak a szolgáltatáscímkékhez lesznek hozzáadva, és a statikus IP-címek végül elavultak lesznek.

Ha hálózati biztonsági csoportokat (NSG-ket) vagy felhasználó által definiált útvonalakat (UDRs) használ a HDInsight-fürt bejövő forgalmának szabályozásához, gondoskodnia kell arról, hogy a fürt képes legyen kommunikálni a kritikus Azure-állapot- és felügyeleti szolgáltatásokkal.  Ezek a szolgáltatások IP-címek régió-specifikus, és ezek közül néhány az összes Azure-régióban vonatkozik. Ha nem egyéni DNS-t használ, akkor engedélyeznie kell az Azure DNS szolgáltatástól érkező forgalmat is.

A következő szakaszok az engedélyezett IP-címeket ismertetik.

## <a name="azure-dns-service"></a>Azure DNS-szolgáltatás

Ha az Azure által biztosított DNS-szolgáltatást használja, engedélyezze a hozzáférést a __168.63.129.16-tól__ az 53-as porton. További információt a [virtuális gépek és a szerepkörpéldányok névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) című dokumentumban talál. Ha egyéni DNS-t használ, hagyja ki ezt a lépést.

## <a name="health-and-management-services-all-regions"></a>Egészségügyi és irányítási szolgáltatások: Minden régió

Az Azure HDInsight állapot- és felügyeleti szolgáltatásainak következő IP-címeiből származó forgalom engedélyezése, amelyek az összes Azure-régióra vonatkoznak:

| Forrás IP-címe | Cél  | Irány |
| ---- | ----- | ----- |
| 168.61.49.99 | \*:443 | Bejövő |
| 23.99.5.239 | \*:443 | Bejövő |
| 168.61.48.131 | \*:443 | Bejövő |
| 138.91.141.162 | \*:443 | Bejövő |

## <a name="health-and-management-services-specific-regions"></a>Egészségügyi és irányítási szolgáltatások: Egyedi régiók

Az Azure HDInsight állapot- és felügyeleti szolgáltatásaihoz tartozó IP-címekről érkező forgalom engedélyezése abban az Azure-régióban, ahol az erőforrások találhatók:

> [!IMPORTANT]  
> Ha a használt Azure-régió nem szerepel a listában, majd használja a [szolgáltatáscímke](hdinsight-service-tags.md) funkciót a hálózati biztonsági csoportok.

| Ország | Régió | Engedélyezett forrás IP-címek | Engedélyezett cél | Irány |
| ---- | ---- | ---- | ---- | ----- |
| Ázsia | Kelet-Ázsia | 23.102.235.122</br>52.175.38.134 | \*:443 | Bejövő |
| &nbsp; | Délkelet-Ázsia | 13.76.245.160</br>13.76.136.249 | \*:443 | Bejövő |
| Ausztrália | Kelet-Ausztrália | 104.210.84.115</br>13.75.152.195 | \*:443 | Bejövő |
| &nbsp; | Délkelet-Ausztrália | 13.77.2.56</br>13.77.2.94 | \*:443 | Bejövő |
| Brazília | Dél-Brazília | 191.235.84.104</br>191.235.87.113 | \*:443 | Bejövő |
| Kanada | Kelet-Kanada | 52.229.127.96</br>52.229.123.172 | \*:443 | Bejövő |
| &nbsp; | Közép-Kanada | 52.228.37.66</br>52.228.45.222 |\*: 443 | Bejövő |
| Kína | Észak-Kína | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*:443 | Bejövő |
| &nbsp; | Kelet-Kína | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*:443 | Bejövő |
| &nbsp; | Kína Észak 2 | 40.73.37.141</br>40.73.38.172 | \*:443 | Bejövő |
| &nbsp; | Kína Keleti 2 | 139.217.227.106</br>139.217.228.187 | \*:443 | Bejövő |
| Európa | Észak-Európa | 52.164.210.96</br>13.74.153.132 | \*:443 | Bejövő |
| &nbsp; | Nyugat-Európa| 52.166.243.90</br>52.174.36.244 | \*:443 | Bejövő |
| Franciaország | Közép-Franciaország| 20.188.39.64</br>40.89.157.135 | \*:443 | Bejövő |
| Németország | Közép-Németország | 51.4.146.68</br>51.4.146.80 | \*:443 | Bejövő |
| &nbsp; | Északkelet-Németország | 51.5.150.132</br>51.5.144.101 | \*:443 | Bejövő |
| India | Közép-India | 52.172.153.209</br>52.172.152.49 | \*:443 | Bejövő |
| &nbsp; | Dél-India | 104.211.223.67<br/>104.211.216.210 | \*:443 | Bejövő |
| Japán | Kelet-Japán | 13.78.125.90</br>13.78.89.60 | \*:443 | Bejövő |
| &nbsp; | Nyugat-Japán | 40.74.125.69</br>138.91.29.150 | \*:443 | Bejövő |
| Dél-Korea | Dél-Korea középső régiója | 52.231.39.142</br>52.231.36.209 | \*:443 | Bejövő |
| &nbsp; | Dél-Korea déli régiója | 52.231.203.16</br>52.231.205.214 | \*:443 | Bejövő
| Egyesült Királyság | Az Egyesült Királyság nyugati régiója | 51.141.13.110</br>51.141.7.20 | \*:443 | Bejövő |
| &nbsp; | Az Egyesült Királyság déli régiója | 51.140.47.39</br>51.140.52.16 | \*:443 | Bejövő |
| Egyesült Államok | USA középső régiója | 13.89.171.122</br>13.89.171.124 | \*:443 | Bejövő |
| &nbsp; | USA keleti régiója | 13.82.225.233</br>40.71.175.99 | \*:443 | Bejövő |
| &nbsp; | USA északi középső régiója | 157.56.8.38</br>157.55.213.99 | \*:443 | Bejövő |
| &nbsp; | USA nyugati középső régiója | 52.161.23.15</br>52.161.10.167 | \*:443 | Bejövő |
| &nbsp; | USA nyugati régiója | 13.64.254.98</br>23.101.196.19 | \*:443 | Bejövő |
| &nbsp; | USA nyugati régiója, 2. | 52.175.211.210</br>52.175.222.222 | \*:443 | Bejövő |
| &nbsp; | Egyesült Arab Emírségek északi | 65.52.252.96</br>65.52.252.97 | \*:443 | Bejövő |

Az Azure Government számára használandó IP-címekről az [Azure Government Intelligence + Analytics](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) dokumentumában talál további információt.

További információt a [Hálózati forgalom szabályozása](hdinsight-plan-virtual-network-deployment.md#networktraffic) című részben talál.

Ha felhasználó által definiált útvonalakat (UDRs) használ, meg kell adnia egy útvonalat, és engedélyeznie kell a kimenő forgalmat a virtuális hálózatról a fenti IP-kre, a következő ugrás "Internet" értékre állítva.

## <a name="next-steps"></a>További lépések

* [Virtuális hálózatok létrehozása Az Azure HDInsight-fürtökhöz](hdinsight-create-virtual-network.md)
* [Hálózati biztonsági csoport (NSG) szolgáltatáscímkéi az Azure HDInsighthoz](hdinsight-service-tags.md)
