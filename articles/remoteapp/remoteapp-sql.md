<properties
   pageTitle="Az SQL Azure és az Azure RemoteApp | Microsoft Azure"
   description="Ismerje meg, hogyan használható együtt az SQL Azure és az Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>


# Az SQL Azure és az Azure RemoteApp

> [AZURE.IMPORTANT]
> Azure RemoteApp hamarosan megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).

Amikor a felhasználók a Windows-alkalmazásaikat a felhőben kívánják üzemeltetni az Azure RemoteApp segítségével, gyakran az adataikat, például az SQL-kiszolgálóikat is át szeretnék telepíteni a felhőbe egy teljes körű felhőalapú telepítés érdekében. Ez lehetővé teszi egy teljesen a felhőben üzemeltetett megoldás megvalósítását, amely bármikor, bármilyen eszköz használatával elérhető az Azure RemoteApp használatával. Az alábbiakban útmutatásokat tartalmazó hivatkozásokat és referenciákat talál, amelyek segítenek ebben a folyamatban.  

## SQL-adatok áttelepítése

Kezdjen a következővel: [SQL Server-adatbázis áttelepítése az Azure SQL Database-be](../sql-database/sql-database-cloud-migrate.md). 

## Az Azure RemoteApp konfigurálása
Windows-alkalmazásait az Azure RemoteAppban üzemeltetheti. Az alábbiakban egy magas szintű részletes útmutatót talál:

1.     Hozza létre az [Azure RemoteApp sablon virtuális gépet](remoteapp-imageoptions.md). 
2.     Telepítse a szükséges alkalmazást a virtuális gépre.
3.     Konfigurálja úgy az alkalmazást, hogy csatlakozzon az SQL Database-hez, és ellenőrizze, hogy működik-e.
4.     Futtassa a Sysprep eszközt, majd állítsa le a virtuális gépet. Rögzítse a virtuális gépet rendszerképként az Azure-ral való használathoz. **Megjegyzés:** Biztosítania kell, hogy az alkalmazás képes fenntartani az adatbázis-kapcsolati információkat a sysprep folyamata során. Ha az alkalmazás nem tudja fenntartani az adatbázis-kapcsolati információkat, az alkalmazás gyártójától tudhatja meg, hogyan adható meg a kapcsolati karakterlánc.
5.     Importálja az egyéni rendszerképet az Azure RemoteApp-címtárba. Ehhez válassza ki az SQL Azure üzemelő példányának földrajzi helyét. 
6.     A fenti sablon használatával telepítsen egy RemoteApp-gyűjteményt ugyanabba az adatközpontba, ahol az SQL Azure üzemelő példánya található, és tegye közzé az alkalmazást. Ha ugyanabban az adatközpontban helyezi üzembe az Azure RemoteAppot, amelyben az SQL Azure üzemelő példánya található, az segít biztosítani a leggyorsabb kapcsolatot és csökkenti a késés mértékét. 

## Az alkalmazás és az SQL konfigurálására vonatkozó megfontolások:
A RemoteApp és az Azure SQL együttes használatakor érdemes figyelembe vennie a következőket:

Ismerje meg, [hogyan konfigurálható egy Azure SQL-adatbázis tűzfala](../sql-database/sql-database-firewall-configure.md). A cikkből szerint „kezdetben az Azure SQL Database-kiszolgáló összes hozzáférését blokkolja a tűzfal. Az Azure SQL Database-kiszolgáló használatának elkezdéséhez be kell lépnie a klasszikus portálra, és meg kell határoznia egy vagy több kiszolgálószintű tűzfalszabályt, amely engedélyezi a hozzáférést az Azure SQL Database-kiszolgálóhoz. A tűzfalszabályok segítségével adja meg, hogy az internet felől melyik IP-címtartományok engedélyezettek, valamint hogy az Azure-alkalmazások megpróbálhatnak-e csatlakozni az Azure SQL Database-kiszolgálóhoz.”

Emellett amikor egy számítógép megpróbál csatlakozni az adatbázis-kiszolgálóhoz az internetről, a tűzfal a kiszolgálószintű és (szükség esetén) az adatbázisszintű tűzfalszabályok teljes készletében ellenőrzi a kérés eredeti IP-címét. „Ha a kérés IP-címe a kiszolgálószinten megadott tűzfalszabályok tartományok egyikébe esik, a rendszer engedélyezi a csatlakozást az Azure SQL Database-kiszolgálóhoz.” Így nemcsak egyes forrás IP-címek, hanem IP-címtartományok is használhatók.

Az IP-címtartomány megadásához kövesse a következő cikkben található utasításokat: [How to: Configure firewall settings on SQL Database using the Azure Portal](../sql-database/sql-database-configure-firewall-settings.md) (Útmutató: Az SQL database tűzfalbeállításainak konfigurálása az Azure Portal segítségével). Az SQL-tűzfalszabályok konfigurálásakor adja meg azon alhálózat IP-címtartományát, amely meg van adva az Azure RemoteApp-gyűjteményhez. Ez lehetővé teszi az ARA-kiszolgálók számára, hogy csatlakozzanak az SQL Database-hez, annak ellenére, hogy dinamikusan hozzárendelt IP-címekkel rendelkeznek.

## Hibaelhárítás
Ha egy Azure RemoteAppban üzemeltetett, egy Azure-ban vagy a helyszínen üzemeltetett SQL Database-hez csatlakozó ügyfélalkalmazás lelassul, annak több oka is lehet.  

- Az eszköz és az Azure közötti hálózati késés magas. A legjobb teljesítmény érdekében váltson át a lehető legjobb és leggyorsabb hálózati kapcsolatra. Általános eszközként az [azurespeed.com](http://azurespeed.com/) webhelyet használhatja az eszköz és az Azure-adatközpont közti késés teszteléséhez.  
- Az Azure RemoteAppban üzemeltetett ügyfélalkalmazás magas terhelés alatt áll. Egy másik számlázási csomag, például a prémium tarifacsomag javítja a teljesítményt. Egy másik trükk az alkalmazás által felhasznált erőforrások megfigyelése: egy aktív munkamenet közben nyomja le a ctrl-alt-end billentyűkombinációt, amely megnyitja az SAS képernyőt, válassza ki a Feladatkezelőt, és vizsgálja meg az alkalmazás erőforrás-felhasználását.
- Az SQL Server magas terhelés alatt áll, vagy nincs optimalizálva. A hibaelhárításhoz kövesse az SQL útmutatásait. 




<!--HONumber=Sep16_HO4-->


