Ellenőrizheti, hogy sikerült-e a kapcsolat a "Get-AzureVNetConnection" parancsmag használatával.

1. A következő parancsmag-példával az értékeket a sajátjaival megegyezően konfigurálhatja. Szóközöket tartalmaz, a virtuális hálózat nevét idézőjelek között kell lennie.

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. A parancsmag futtatása után tekintse meg az értékeket. Az alábbi példában a kapcsolati állapota jeleníti meg, mint a "Csatlakoztatott", és láthatja a bemenő és kimenő bájtokat.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal