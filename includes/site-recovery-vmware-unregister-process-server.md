A folyamatkiszolgálók regisztrációjának visszavonásához szükséges lépések eltérőek lehetnek attól függően, hogy milyen a konfigurációs kiszolgálóval való kapcsolatuk állapota.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Csatlakoztatott állapotban lévő folyamatkiszolgáló regisztrációjának visszavonása

1. Távolról csatlakozzon a folyamatkiszolgálóhoz rendszergazdaként.
2. Indítsa el a **Vezérlőpultot**, és nyissa meg a **Programok > Program eltávolítása** elemet
3. Távolítsa el a **Microsoft Azure Site Recovery Configuration/Process Server** programot
4. A 3. lépés befejezése után eltávolíthatja a **Microsoft Azure Site Recovery Configuration/Process Server Dependencies** elemet.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Leválasztott állapotban lévő folyamatkiszolgáló regisztrációjának visszavonása

> [!WARNING]
> Ha nem lehet helyreállítani azt a virtuális gépet, amelyen a folyamatkiszolgáló telepítve van, használja az alábbi lépéseket.

1. Jelentkezzen be a konfigurációs kiszolgálóra rendszergazdaként.
2. Nyisson meg egy rendszergazdai parancssort, és keresse meg a `%ProgramData%\ASR\home\svsystems\bin` könyvtárat.
3. Most futtassa a parancsot.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Ez véglegesen törli a folyamatkiszolgáló adatait a rendszerből.


<!--HONumber=Feb17_HO1-->


