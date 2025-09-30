
# Get-M365-MFA-Status.ps1
# Erstellt einen Bericht über den MFA-Status aller Benutzer in Microsoft 365

# Microsoft Graph laden
Connect-MgGraph -Scopes "User.Read.All", "Directory.Read.All"

# Alle Benutzer abrufen
$users = Get-MgUser -All

# MFA-Status prüfen
$mfaReport = foreach ($user in $users) {
    $methods = Get-MgUserAuthenticationMethod -UserId $user.Id
    [PSCustomObject]@{
        Name      = $user.DisplayName
        Benutzer  = $user.UserPrincipalName
        MFA       = if ($methods.Count -gt 0) { "Ja" } else { "Nein" }
    }
}

# Ergebnis exportieren
$mfaReport | Export-Csv -Path ".\MFA-Report.csv" -NoTypeInformation -Encoding UTF8
Write-Host "✅ MFA-Bericht erstellt: MFA-Report.csv"
