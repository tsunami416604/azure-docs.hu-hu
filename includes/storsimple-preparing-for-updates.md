<!--author=jgerend last changed: 03/16/16-->

## <a name="preparing-for-updates"></a>Frissítések előkészítése
Szüksége lesz a következő lépésekkel vizsgálata és a frissítés telepítése előtt:

1. Felhő alapú pillanatképet készít, az eszközadatok.
2. Győződjön meg arról, hogy a vezérlő rögzített IP-címei irányítható és csatlakozni tud-e az internethez. A rögzített IP-címek használandó frissítheti az eszközre a szolgáltatást. A következő parancsmag futtatásával a tartományvezérlők a Windows PowerShell felületén, az eszköz tesztelheti:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
    **Példa a kimenetre kapcsolat tesztelése során rögzített IP-címeket tud csatlakozni az internethez**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Miután ezek manuális előzetes ellenőrzése sikeresen befejeződött, lépne vizsgálata, és telepítse a frissítéseket.

