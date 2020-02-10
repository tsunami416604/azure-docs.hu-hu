---
title: 'VPN Gateway: VPN-ügyfél az OpenVPN protokoll P2S kapcsolataihoz: Azure AD-hitelesítés'
description: A P2S VPN használatával kapcsolódhat a VNet az Azure AD-hitelesítés használatával
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/07/2020
ms.author: alzam
ms.openlocfilehash: 045d1cad130adad34d74009b34b193ce0d3d4dc9
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110554"
---
# <a name="configure-a-vpn-client-for-p2s-openvpn-protocol-connections-azure-ad-authentication"></a>VPN-ügyfél konfigurálása a P2S OpenVPN protokoll kapcsolataihoz: Azure AD-hitelesítés

Ebből a cikkből megtudhatja, hogyan konfigurálhat VPN-ügyfelet egy virtuális hálózathoz pont – hely VPN és Azure Active Directory hitelesítés használatával való csatlakozáshoz. Az Azure AD-vel való kapcsolat és hitelesítés előtt először konfigurálnia kell az Azure AD-bérlőt. További információ: [Azure ad-bérlő konfigurálása](openvpn-azure-ad-tenant.md).

> [!NOTE]
> Az Azure AD-hitelesítés csak az OpenVPN® protokoll-kapcsolatok esetén támogatott.
>

## <a name="profile"></a>Ügyféloldali profilok használata

A csatlakozáshoz le kell töltenie az Azure VPN-ügyfelet, és konfigurálnia kell egy VPN-ügyféloldali profilt minden olyan számítógépen, amely csatlakozni szeretne a VNet. Létrehozhat egy ügyféloldali profilt a számítógépen, exportálhatja, majd importálhatja további számítógépekre.

### <a name="to-download-the-azure-vpn-client"></a>Az Azure VPN-ügyfél letöltése

Ezzel a [hivatkozással](https://go.microsoft.com/fwlink/?linkid=2117554) töltheti le az Azure VPN-ügyfelet.

### <a name="cert"></a>Tanúsítvány alapú ügyféloldali profil létrehozása

Tanúsítványalapú profillal való munka esetén győződjön meg arról, hogy a megfelelő tanúsítványok telepítve vannak az ügyfélszámítógépen. További információ a tanúsítványokról: [Ügyféltanúsítványok telepítése](point-to-site-how-to-vpn-client-install-azure-cert.md).

  ![tanúsítvány](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>RADIUS-ügyfél profiljának létrehozása

  ![RADIUS-](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> A kiszolgáló titkos kulcsa exportálható a P2S VPN-ügyfél profiljában.  Az ügyféloldali profilok exportálásával kapcsolatban [itt](about-vpn-profile-download.md)talál útmutatást.
>

### <a name="export"></a>Ügyféloldali profil exportálása és terjesztése

Ha van egy működő profilja, és el kell terjesztenie más felhasználóknak, a következő lépésekkel exportálhatja:

1. Jelölje ki az exportálni kívánt VPN-ügyféloldali profilt, majd válassza a **...** , majd az **Exportálás**lehetőséget.

    ![exportálása](./media/openvpn-azure-ad-client/export/export1.jpg)

2. Válassza ki azt a helyet, ahová a profilt menteni szeretné, hagyja meg a fájl nevét, majd válassza a **Mentés** lehetőséget az XML-fájl mentéséhez.

    ![exportálása](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>Ügyféloldali profil importálása

1. A lapon válassza az **Importálás**lehetőséget.

    ![importálása](./media/openvpn-azure-ad-client/import/import1.jpg)

2. Keresse meg a profil XML-fájlját, és jelölje ki. A fájl kijelölése után válassza a **Megnyitás**lehetőséget.

    ![importálása](./media/openvpn-azure-ad-client/import/import2.jpg)

3. Adja meg a profil nevét, majd válassza a **Mentés**lehetőséget.

    ![importálása](./media/openvpn-azure-ad-client/import/import3.jpg)

4. Válassza a **Csatlakozás** lehetőséget a VPN-hez való csatlakozáshoz.

    ![importálása](./media/openvpn-azure-ad-client/import/import4.jpg)

5. A csatlakozás után az ikon zöldre vált, és a rendszer a **csatlakozást**.

    ![importálása](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>Ügyféloldali profil törlése

1. Válassza ki a törölni kívánt ügyfél-profil melletti ellipsziseket. Ezután válassza az **Eltávolítás**lehetőséget.

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. Válassza az **Eltávolítás** elemet a törléshez.

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>Kapcsolatok létrehozása

1. A lapon válassza a **+** , majd a **+ Hozzáadás**lehetőséget.

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create1.jpg)

2. Adja meg a kapcsolatok adatait. Ha nem tudja biztosan az értékeket, forduljon a rendszergazdához. Az értékek kitöltése után válassza a **Mentés**lehetőséget.

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create2.jpg)

3. Válassza a **Csatlakozás** lehetőséget a VPN-hez való csatlakozáshoz.

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create3.jpg)

4. Válassza ki a megfelelő hitelesítő adatokat, majd kattintson a **Continue (folytatás**) gombra.

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create4.jpg)

5. A sikeres csatlakozás után az ikon zöldre vált, és a rendszer a **csatlakozást**fogja mondani.

    ![kapcsolat](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>Automatikus kapcsolódás

Ezekkel a lépésekkel konfigurálhatja a kapcsolatot úgy, hogy automatikusan kapcsolódjon az Always-on.

1. A VPN-ügyfél kezdőlapján válassza a **VPN-beállítások**lehetőséget.

    ![automatikus](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. Válassza az **Igen** lehetőséget az alkalmazások váltása párbeszédablakban.

    ![automatikus](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. Győződjön meg arról, hogy a beállítani kívánt kapcsolat még nincs csatlakoztatva, majd jelölje ki a profilt, majd jelölje be az **Automatikus csatlakozás** jelölőnégyzetet.

    ![automatikus](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. A VPN-kapcsolat indításához válassza a **Csatlakozás** lehetőséget.

    ![automatikus](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>Kapcsolatok problémáinak diagnosztizálása

1. A kapcsolódási problémák diagnosztizálásához használhatja a **Diagnosztizálás** eszközt. Válassza a **...** elemet a diagnosztizálni kívánt VPN-kapcsolat mellett a menü megjelenítéséhez. Ezután válassza a **Diagnosztizálás**lehetőséget.

    ![diagnosztizálása](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. A **kapcsolatok tulajdonságai** lapon válassza a **diagnosztika futtatása**lehetőséget.

    ![diagnosztizálása](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. Jelentkezzen be a hitelesítő adataival.

    ![diagnosztizálása](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. A diagnosztika eredményeinek megtekintése.

    ![diagnosztizálása](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>GYIK

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>Hogyan DNS-utótagokat hozzáadni a VPN-ügyfélhez?

Megváltoztathatja a letöltött profil XML-fájlját, és hozzáadhatja a **\<dnssuffixes >\<dnssufix > \</dnssufix >\</dnssuffixes >** címkét

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

## <a name="next-steps"></a>Következő lépések

További információ: [Azure Active Directory-bérlő létrehozása az Azure ad-hitelesítést használó P2S nyitott VPN-kapcsolatokhoz](openvpn-azure-ad-tenant.md).
