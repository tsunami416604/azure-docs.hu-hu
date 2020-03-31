---
title: Azure-bástya – problémamegoldás | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan háríthatja el az Azure-bástya.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: cherylmc
ms.openlocfilehash: de112ff441bb53a0b3bc7f4ffa4456f1c241682c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73512950"
---
# <a name="troubleshoot-azure-bastion"></a>Az Azure-bastion hibáinak elhárítása

Ez a cikk bemutatja, hogyan háríthatja el az Azure-bástya.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Nem lehet nsg-t létrehozni az AzureBastionSubnet-en

**K:** Amikor nstion alhálózaton próbálok nsg-t létrehozni, a következő hibaüzenet jelenik meg: *"A hálózati biztonsági csoport <NSG name> nem rendelkezik az Azure Bastion Subnet AzureBastionSubnet szükséges szabályaival"*.

**A.** Ha nsg-t hoz létre és alkalmaz az *AzureBastionSubnet-re,* győződjön meg arról, hogy az nsg-ben hozzáadta a következő szabályokat. Ha nem adja hozzá ezeket a szabályokat, az NSG létrehozása/frissítése sikertelen lesz.

1. Vezérlősík-kapcsolat – Bejövő a 443-ason a GatewayManager-től
2. Diagnosztikai naplózás és egyéb – 443-as azurecloud-alapú kimenő (a szolgáltatáscímkében található regionális címkék még nem támogatottak.)
3. Cél virtuális gép – 3389 és 22 a VirtualNetwork számára

Az NSG-szabályokra példa a [rövid útmutató sablonban.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion)
További információ: [NSG útmutató az Azure Bastion.](bastion-nsg.md)

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Nem lehet használni az SSH-kulcsot az Azure Bastion használatával

**K:** Amikor megpróbálom böngészni az SSH kulcsfájlt, a következő hibaüzenet jelenik meg: *"Az SSH titkos kulcsának a -----BEGIN RSA PRIVATE KEY------val kell kezdődnie----- és -----END RSA PRIVATE KEY-----" .*

**A.** Az Azure Bastion támogatja csak RSA SSH kulcsok, ebben az időpontban. Győződjön meg arról, hogy az SSH RSA-titkos kulcsaként tallózik, és nyilvános kulcs van kiépítve a célvirtuális gépen. 

Például a következő paranccsal új RSA SSH-kulcsot hozhat létre:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com"**

Kimenet:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Nem lehet bejelentkezni a Windows tartományhoz csatlakozó virtuális gépembe

**K:** Nem tudok csatlakozni a tartományhoz csatlakozó Windows virtuális gépemhez.

**A.** Az Azure Bastion támogatja a tartományhoz csatlakozott virtuális gép bejelentkezést csak felhasználónév-jelszó alapú tartománybejelentkezéshez. Amikor megadja a tartományi hitelesítő adatokat az Azureusername@domainPortalon, használja az upn ( ) formátumhelyett *domain\felhasználónév* formátumban a bejelentkezéshez. Ez a tartományhoz csatlakozott vagy hibrid csatlakozású (tartományhoz csatlakozott, valamint az Azure AD-hez csatlakozott) virtuális gépek támogatott. Nem támogatott az Azure AD-hez csatlakozott virtuális gépek.

## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Fájlátviteli problémák

**K:** Az Azure Bastion támogatja a fájlátvitelt?

**A.** A fájlátvitel jelenleg nem támogatott. Dolgozunk a támogatás hozzáadásán.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Fekete képernyő az Azure Portalon

**K:** Amikor az Azure-bástyával próbálok csatlakozni, fekete képernyő jelenik meg az Azure Portalon.

**A.** Ez akkor fordul elő, ha hálózati kapcsolati probléma merül fel a webböngésző és az Azure Bastion között (az ügyfél internetes tűzfala blokkolhatja a WebSockets-forgalmat vagy hasonlót), vagy az Azure Bastion és a célvirtuális gép között. A legtöbb esetben egy NSG alkalmazott vagy az AzureBastionSubnet, vagy a cél virtuális gép alhálózat, amely blokkolja az RDP/SSH forgalmat a virtuális hálózatban. Engedélyezze a WebSocket-forgalmat az ügyfél internetes tűzfalán, és ellenőrizze az NSG-ket a cél virtuálisgép-alhálózaton.

## <a name="next-steps"></a>További lépések

További információt a [Bástya GYIK című témakörben talál.](bastion-faq.md)