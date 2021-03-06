NPAPI DLL for calling DLL functions inside JavaScript.
======================================================

Sample
------
    <embed type="application/x-win32api-dynamic-call" id="p" hidden="true" />
    <script type="text/javascript">
    function foo(){
        var r = "";
        var plugin = document.getElementById( "p" );

        // import functions
        var MessageBox = plugin.import( "user32.dll", "INT MessageBoxW( DWORD, LPCWSTR, LPCWSTR, UINT )" ); 
        var GetWindowsDirectory = plugin.import( "kernel32.dll", "UINT GetWindowsDirectoryW( LPWSTR, UINT )" );
        var GetDiskFreeSpace = plugin.import( "kernel32.dll", "BOOL GetDiskFreeSpaceW( LPCWSTR, LPDWORD, LPDWORD, LPDWORD, LPDWORD )" );

        var MB_ICONINFORMATION = 64;

        // basic usage
        if( MessageBox ){
            MessageBox( 0, "hello, world", "chrome", MB_ICONINFORMATION );
        }

        // example of retrieving integer
        if( GetDiskFreeSpace ){
            var SectorsPerCluster = 0 , BytesPerSector = 1, NumberOfFreeClusters = 1, TotalNumberOfClusters = 1;
            if( GetDiskFreeSpace( "C:\\", SectorsPerCluster, BytesPerSector, NumberOfFreeClusters, TotalNumberOfClusters ) ){
                r = "SectorsPerCluster:" + GetDiskFreeSpace.arg( 1 ) + "\nBytesPerSector:" + GetDiskFreeSpace.arg( 2 ) +
                    "\nNumberOfFreeClusters:" + GetDiskFreeSpace.arg( 3 ) + "\nTotalNumberOfClusters:" + GetDiskFreeSpace.arg( 4 ) + "\n";
            };
        }

        // example of retrieving string
        var buf = new Array( 257 ).join( " " );// space * 256
        if( GetWindowsDirectory ){
            GetWindowsDirectory( buf, buf.length );
            r += "Windows directory:'" + GetWindowsDirectory.arg( 0 ) + "'\n";
        }

        if( MessageBox ){
            MessageBox( 0, r, "Chrome", MB_ICONINFORMATION );
        }

        // using callback
        var maxEnumCount = 10;
        r = "hwnd : title\n";
        var EnumWindows = plugin.import( "user32.dll", "BOOL EnumWindows( CALLBACK, DWORD )" );
        var GetWindowText = plugin.import( "user32.dll", "INT GetWindowTextW( DWORD, LPWSTR, INT )" );
        var callback = plugin.callback( 
            function ( hwnd, lparam ){
                var buf = new Array( 257 ).join( " " );// space * 256
                if( GetWindowText( hwnd, buf, 256 ) ){
                    r += hwnd + " : " + GetWindowText.arg( 1 ) + "\n";
                    maxEnumCount--;
                }
                return maxEnumCount > 0;
            },
            "BOOL (DWORD, DWORD)"
        );
        EnumWindows( callback, 0x1234 );
        alert( r );
    }
    </script>
    

License
-------
the MIT License


Author
------
Yosuke HASEGAWA  <[utf-8.jp](http://utf-8.jp/)>




