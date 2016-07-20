<properties
   pageTitle="Az OneDrive Vállalati verzió és az Azure RemoteApp integrálása | Microsoft Azure"
   description="Ismerje meg, hogyan használható együtt a OneDrive Vállalati verzió és az Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="03/31/2016"
   ms.author="elizapo"/>

# Az OneDrive Vállalati verzió és az Azure RemoteApp integrálása

A OneDrive Vállalati verziót fájltárolóként használhatja az Azure RemoteApp szolgáltatással. A OneDrive Vállalati verzió kiváló módja annak, hogy szinkronizálva tartsa a fájljait az összes eszközén és munkaterületén. A [felhasználói profillemeze](remoteapp-upd.md) (UPD) az a hely, ahol a felhasználó tárolhatja az Azure RemoteApphoz kapcsolódó fájljait, azonban ezek a fájlok csak az Azure RemoteAppon keresztül érhetők el. A OneDrive Vállalati verzió ezzel szemben lehetővé teszi a felhasználó számára, hogy bárhonnan és bármikor elérje a fájlokat, az Azure RemoteApp használata nélkül is. Ez a cikk a támogatott OneDrive Vállalati verziókat és azokat a módokat ismerteti, amelyekkel a rendszergazdák beállíthatják a OneDrive Vállalati verziót az Azure RemoteAppban.

## A OneDrive összes verziója támogatott?

A OneDrive-nak két verziója van: a OneDrive és a OneDrive Vállalati verzió. Az Azure RemoteApp csak a OneDrive Vállalati verziót támogatja. A személyes OneDrive is működik, azonban hivatalosan nem támogatott. Emellett az Azure RemoteApp szolgáltatás (és a RDSH-/Citrix-/terminálkiszolgálók) csak a legújabb OneDrive Vállalati verziót, más néven a Next Gen Sync-ügyfelet támogatják.

>[AZURE.NOTE]  A OneDrive (fogyasztói/személyes kiadás) nem támogatott az Azure RemoteAppban. Nem minden OneDrive Vállalati verzió támogatott, mivel nem mindegyik rendelkezik tanúsítvánnyal a Windows Serveren való működéshez. Annak ellenére, hogy látszólag az új ügyfél (Next Gen Sync-ügyfél) és a régebbi Groove-verziók is jól működnek az Azure RemoteAppban, mint ahogy azt a [https://support.microsoft.com/en-us/kb/2965687](https://support.microsoft.com/kb/2965687) cikk ismerteti, a régebbi szinkronizálási motorok nem minden funkciója működik a Citrix-/terminálkiszolgálókon (Windows Server rendszeren).

## Mik a OneDrive Vállalati verzió különböző telepítési lehetőségei?

- **A OneDrive Vállalati verzió szinkronizálási motor hagyományos telepítése:** Ez a lehetőség jelenleg nem támogatott Azure RemoteApp, az RDSH és a Citrix üzemelő példányaiban.
- **„A OneDrive Vállalati verzió „virtualizálása”/átirányítása a helyi gazdag ügyféltől a munkamenetbe: **Ha a OneDrive szinkronizálását végzi egy ügyféleszközön lévő mappába, a meghajtó választásakor [átirányíthatja](remoteapp-redirection.md) az adott meghajtót az Azure RemoteAppba – ennek a meghajtónak azonosnak kell lennie az összes felhasználó ügyfele esetében, és a OneDrive-ot szinkronizálni kell egy mappába a meghajtón. Ha a felhasználók bármely más ügyfélről férnek hozzá a RemoteApphoz, lehetséges, hogy ezek a fájlok nem érhetők el (megkerülő megoldás: a fájlokat mindig elérhetik a OneDrive online verziójával). 
- **A OneDrive Vállalati verzió megjelenítése meghajtóként az Azure RemoteApp környezetben a fájlok gyorsítótárazása/szinkronizálása nélkül:** (a OneDrive Vállalati verzió http URL-címének leképezése egy virtuális gépen lévő meghajtóra) A OneDrive Vállalati verzió RDSH környezetben lévő hálózati meghajtóra való leképezése támogatott. Forgatókönyvek, amelyekben ez használható: 
    - Ha (tárhely nélküli) vékonyklienseket használ az Azure RemoteApp eléréséhez – Egy alkalmazásnak a OneDrive Vállalati verzión tárolt fájlokra van szüksége, de azt szeretné, hogy azok helyi fájloknak „látszódjanak”, és a rendszergazda nem szeretné szinkronizálni a fájlokat egy virtuális gépre.
    - Ha sok nagy méretű fájl található a OneDrive Vállalati verzión, amelyek ki lettek választva szinkronizálásra. A szinkronizálás számítási feladatai miatt előfordulhat, hogy nem lesz az összes fájl szinkronizálva, amikor a felhasználó használni kívánja őket. Emellett ha a fájlok teljes mérete meghaladja az 50 GB-ot, akkor nem tárolhatók a felhasználói profillemezen.

A fenti forgatókönyvekben a rendszergazdák választhatják a virtuális gépen lévő meghajtó leképezésének lehetőségét a felhasználó OneDrive Vállalati verziójának http URL-címére. Néhány lehetőség ennek engedélyezésére:

- Az Office bináris fájljainak tárolása a rendszerképen, és a OneDrive Vállalati verzió szinkronizálási motor aktiválásának mellőzése.
- Nincsenek Office bináris fájlok a rendszerképen – ebben az esetben egy előfeltétel, az Asztali élmény csomag megléte szükséges. Pontosabban a WebClientet (más néven WebDAV-ot) az Asztali élmény csomag részeként kell telepíteni. 

### Az Asztali élmény csomag telepítése Windows Server 2012 R2 rendszeren
Az Asztali élmény csomag telepítése: 

1. A Kiszolgálókezelőben kattintson a **Kezelés -> Szerepkörök és szolgáltatások hozzáadása** elemre.
2. Kattintson a **Szolgáltatások**, majd a **Felhasználói felületek és infrastruktúra -> Asztali élmény** elemre.

### Meghajtó leképezése a OneDrive Vállalati verzió URL-címére

Kövesse a következő támogatási cikk utasításait: [https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)
 
A telepítés fontos lépése, hogy mindenképpen jelölje be a **Bejelentkezve szeretnék maradni** jelölőnégyzetet.

Az utasítások összefoglalva a következők:

1.  Keresse meg a meghajtó URL-címét. A meghajtó leképezéséhez használt URL-cím akkor jelenik meg, amikor a OneDrive Vállalati verzió kezdőkönyvtárához navigál az interneten. Példa:
 
    https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.  Nyissa meg az URL-címet egy böngészővel a RemoteApp-munkamenetben, majd jelölje be a **Bejelentkezve szeretnék maradni** jelölőnégyzetet, mielőtt bejelentkezik a fiókja URL-címére.
3.  Nyissa meg a Windows Intézőt, és képezzen le egy meghajtót a fenti URL-címre. Ha az URL nem oldható fel, a rövidebb változat használható:
    
    https://microsoft-my.sharepoint.com/personal/alias_microsoft_com. 

    Ez azonnal létrehozza a leképezett meghajtót, amely a következőképpen néz ki:
 
    ![OneDrive Vállalati verzió leképezett hálózati meghajtóként](./media/remoteapp-onedrive/ra-mappeddrive.png)


<!--HONumber=Jun16_HO2-->


