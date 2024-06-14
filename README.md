# Skrypt PowerShell
# Zmienna bazowa dla głównej ścieżki
$baseDir = "C:\sciezka\do\bazowego\katalogu"

# Zmienna z nazwą nowego brancha
$newBranchName = "nowy-branch"

# Pobierz nazwy katalogów z pliku
$katalogi = Get-Content -Path "C:\sciezka\do\katalogi.txt"

foreach ($katalog in $katalogi) {
    # Sklej główną ścieżkę z nazwą katalogu
    $fullPath = Join-Path -Path $baseDir -ChildPath $katalog

    if (Test-Path $fullPath) {
        Set-Location -Path $fullPath
        if (Test-Path -Path "$fullPath\.git") {
            Write-Host "Przełączam branch na 'dev' w katalogu $fullPath"
            git checkout dev
            if ($LASTEXITCODE -eq 0) {
                Write-Host "Pobieram najnowsze zmiany dla brancha 'dev' w katalogu $fullPath"
                git pull origin dev
                if ($LASTEXITCODE -eq 0) {
                    Write-Host "Tworzę nowy branch '$newBranchName' w katalogu $fullPath"
                    git checkout -b $newBranchName
                } else {
                    Write-Host "Nie udało się pobrać najnowszych zmian dla brancha 'dev' w katalogu $fullPath"
                }
            } else {
                Write-Host "Nie udało się przełączyć brancha na 'dev' w katalogu $fullPath"
            }
        } else {
            Write-Host "Katalog $fullPath nie jest repozytorium Git"
        }
    } else {
        Write-Host "Katalog $fullPath nie istnieje"
    }
}

# Powrót do początkowego katalogu
Set-Location -Path $PSScriptRoot
