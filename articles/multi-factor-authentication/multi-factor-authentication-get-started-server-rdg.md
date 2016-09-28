<properties 
    pageTitle="Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló"
    description="Ez az Azure Multi-Factor Authentication-oldal segítséget nyújt a távoli asztali (RD) átjáró és a RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló telepítéséhez."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>


# Távoli asztali átjáró és RADIUS-t használó Azure Multi-Factor Authentication-kiszolgáló

Számos esetben a távoli asztali átjáró a helyi NPS használatával hitelesíti a felhasználókat. Ez a dokumentum ismerteti a távoli asztali átjáró RADIUS-kéréseinek továbbítását (a helyi NPS-en keresztül) a Multi-Factor Authentication-kiszolgálónak.

A Multi-Factor Authentication-kiszolgálót külön kiszolgálóra kell telepíteni, amely a RADIUS-kérést proxykapcsolaton keresztül visszaadja az NPS-nek a távoli asztali átjárókiszolgálón. Miután az NPS ellenőrzi a felhasználónevet és a jelszót, választ küld a Multi-Factor Authentication-kiszolgálónak, amely végrehajtja a hitelesítés második tényezőjét, mielőtt az eredményt visszaküldi az átjárónak.





## A távoli asztali átjáró konfigurálása

A távoli asztali átjárót úgy kell konfigurálni, hogy a RADIUS-hitelesítést egy Azure Multi-Factor Authentication-kiszolgálónak küldje el. Ha telepítette és konfigurálta a távoli asztali átjárót, és az működik, nyissa meg a távoli asztali átjáró tulajdonságait. Nyissa meg a Távoli asztali kapcsolatengedélyezési házirendek tárolója lapot, és módosítsa a beállításokat, hogy NPS-t futtató központi kiszolgálót használjon NPS-t futtató helyi kiszolgáló helyett. Adjon hozzá egy vagy több Azure Multi-Factor Authentication-kiszolgálót RADIUS-kiszolgálóként, és adjon meg egy közös titkos kulcsot minden kiszolgáló számára.





## Az NPS konfigurálása

A távoli asztali átjáró NPS használatával küldi a RADIUS-kérést az Azure Multi-Factor Authentication számára. Az időtúllépést módosítani kell, hogy elkerülhető legyen a távoli asztali átjáró időtúllépése a többtényezős hitelesítés befejezése előtt. Az NPS konfigurálásához kövesse az alábbi lépéseket.

1. Az NPS-ben bontsa ki a RADIUS-ügyfelek és -kiszolgálók menüt a bal oldali oszlopban, és kattintson a Távoli RADIUS-kiszolgálócsoportok elemre. Nyissa meg a TS GATEWAY SERVER GROUP tulajdonságait. Szerkessze a megjelenő RADIUS-kiszolgáló(ka)t, majd nyissa meg a Terheléselosztás lapot. Módosítsa a „Válasz nélküli másodpercek száma a kiszolgáló nem elérhetővé nyilvánítása előtt” és a „Két kérelem között eltelt másodpercek száma a kiszolgáló nem elérhetővé nyilvánítása előtt” beállítást 30–60 másodpercre. Kattintson a Hitelesítés/Fiók fülre, és győződjön meg arról, hogy a megadott RADIUS-portok egyeznek azokkal a portokkal, amelyeken a Multi-Factor Authentication-kiszolgáló figyel.
2. Az NPS-t is konfigurálni kell, hogy fogadja az Azure Multi-Factor Authentication-kiszolgálóról visszaküldött RADIUS-hitelesítéseket. Kattintson a RADIUS-ügyfelek elemre a bal oldali menüben. Adja hozzá az Azure Multi-Factor Authentication-kiszolgálót RADIUS-ügyfélként. Válasszon egy rövid nevet, és adjon meg egy közös titkos kulcsot.
3. Bontsa ki a Házirendek szakaszt a bal oldali panelen, és kattintson a Kapcsolatkérelem-házirendek elemre. Ennek tartalmaznia kell egy TS GATEWAY AUTHORIZATION POLICY nevű kapcsolatkérelem-házirendet, amely a távoli asztali átjáró konfigurálásakor lett létrehozva. Ez a házirend továbbítja a RADIUS-kéréseket a Multi-Factor Authentication-kiszolgálónak.
4. A házirend másolásával hozzon létre egy újat. Az új házirendben adjon hozzá egy feltételt, amely egyezteti az ügyfél rövid nevét az Azure Multi-Factor Authentication-kiszolgáló RADIUS-ügyfelének a fenti, 2. lépésben beállított rövid nevével. Módosítsa a hitelesítésszolgáltatót a helyi számítógépre. Ez a házirend biztosítja, hogy amikor RADIUS-kérés érkezik az Azure Multi-Factor Authentication-kiszolgálóról, a hitelesítés helyben történik, ahelyett hogy a rendszer visszaküldené a RADIUS-kérést az Azure Multi-Factor Authentication-kiszolgálónak, ami hurokállapothoz vezetne. A hurokállapot megakadályozása érdekében ezt az új házirendet a Multi-Factor Authentication-kiszolgálónak továbbító eredeti házirend FÖLÉ kell elhelyezni.

## Az Azure Multi-Factor Authentication konfigurálása


--------------------------------------------------------------------------------



Az Azure Multi-Factor Authentication-kiszolgáló RADIUS-proxyként van konfigurálva a távoli asztali átjáró és az NPS között.  A távoli asztali átjárókiszolgálótól eltérő tartományhoz csatlakoztatott kiszolgálóra kell telepíteni. Az Azure Multi-Factor Authentication-kiszolgáló konfigurálásához kövesse az alábbi eljárást.

1. Nyissa meg az Azure Multi-Factor Authentication-kiszolgálót, és kattintson a RADIUS-hitelesítés ikonra. Jelölje be a RADIUS-hitelesítés engedélyezése jelölőnégyzetet.
2. Az Ügyfelek lapon győződjön meg arról, hogy a portok egyeznek az NPS-ben konfiguráltakkal, és kattintson a Hozzáadás... gombra. Adja hozzá a távoli asztali átjáró kiszolgálójának IP-címét, az alkalmazás nevét (nem kötelező) és a közös titkos kulcsot. A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication-kiszolgálón és a távoli asztali átjárón.
3. Kattintson a Cél fülre, és jelölje be a RADIUS-kiszolgálók választógombot.
4. Kattintson a Hozzáadás... gombra. Adja meg az IP-címet, a közös titkos kulcsot és az NPS-kiszolgáló portjait. Ha nem központi NPS-t használ, a RADIUS-ügyfél és a RADIUS-cél azonos. A közös titkos kulcsnak egyeznie kell az NPS-kiszolgáló RADIUS-ügyfél szakaszában beállítottal.

![Radius-hitelesítés](./media/multi-factor-authentication-get-started-server-rdg/radius.png)



<!--HONumber=Sep16_HO4-->


