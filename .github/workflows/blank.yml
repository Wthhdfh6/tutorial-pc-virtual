name: Tailscale Direct Windows RDP

on:
  workflow_dispatch:

jobs:
  build-rdp:
    runs-on: windows-latest
    steps:
      - name: Enable Remote Desktop
        run: |
          Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0
          Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
          
      - name: Set RDP Password
        run: |
          net user runneradmin "RdpPass123456!" /add
          net localgroup administrators runneradmin /add

      - name: Install and Connect Tailscale
        shell: powershell
        run: |
          Invoke-WebRequest -Uri "https://pkgs.tailscale.com/stable/tailscale-setup-latest.exe" -OutFile "tailscale.exe"
          Start-Process -FilePath ".\tailscale.exe" -ArgumentList "/quiet" -Wait
          & "C:\Program Files\Tailscale\tailscaled.exe" --state=mem: &
          Start-Sleep -Seconds 3
          & "C:\Program Files\Tailscale\tailscale.exe" up --authkey=${{ secrets.TAILSCALE_AUTHKEY }}

      - name: Keep Alive RDP
        run: |
          $i = 0
          while ($i -lt 360) {
            Start-Sleep -Seconds 60
            $i++
          }
