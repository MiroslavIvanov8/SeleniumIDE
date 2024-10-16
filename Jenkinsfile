pipeline {
	agent any

    environment{
        CHROME_VERSION = '127.0.6533.73'
        CHROMEDRIVER_VERSION = '127.0.6533.73'
        CHROME_INSTALL_PATH = 'C:\\Program Files (x86)\\Google\\Chrome\\Application'
        CHROMEDRIVER_PATH = 'C:\\Program Files (x86)\\Google\\Chrome\\Application\\chromedriver.exe'
    }
	stages {
		stage('Checkout the code') {
			steps {
			 git branch: 'master', url: 'https://github.com/MiroslavIvanov8/SeleniumIDE'
		     }
	    }
        stage('Download Choco') {
            steps {
                bat '''@echo off
            :: Enable TLS 1.2 for downloading Chocolatey
            powershell -Command "Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12; iex ((New-Object System.Net.WebClient).DownloadString(\'https://community.chocolatey.org/install.ps1\'))"

            :: Check if Chocolatey was installed correctly
            choco --version
            '''
            }
        }
        stage('Set up .NET Core') {
			steps {
                bat '''
                echo Installing .NET SDK 6.0
                choco install dotnet-sdk -y --version=6.0.100
                '''
			}
	    }
        stage('Uninstal Current Chrome') {
			steps {
                bat '''
                echo Uninstalling current Google Chrome
                choco uninstall googlechrome -y  
                '''
			}
	    }
        stage('Install specific version of Chrome') {
			steps {
                bat '''
                echo Installing Google Chrome version %CHROME_VERSION%
                choco install googlechrome --version=%CHROME_VERSION% -y --allow-downgrade --ignore-checksums
                '''
			}
	    }
        stage('Download and Install ChromeDriver') {
            steps {
                bat '''
                echo Downloading ChromeDriver version %CHROMEDRIVER_VERSION%
                powershell -command "Invoke-WebRequest -Uri https://chromedriver.storage.googleapis.com/%CHROMEDRIVER_VERSION%/chromedriver_win32.zip -OutFile chromedriver.zip -UseBasicParsing"
                powershell -command "Expand-Archive -Path chromedriver.zip -DestinationPath ."
                powershell -command "Move-Item -Path .\\chromedriver.exe -Destination '%CHROME_INSTALL_PATH%\\chromedriver.exe' -Force"
                '''
            }
        }
        stage('Restore dependencies') {
			steps {
                bat 'dotnet restore SeleniumIde.sln'
			}
	    }
        stage('Build') {
			steps {
                bat 'dotnet build SeleniumIde.sln -- configuration Release'
			}
	    }
        stage('Execute Tests') {
			steps {
                bat 'dotnet test SeleniumIde.sln --logger "trx;LogFileName=TestResult.trx"'
			}
	    }
		
    }
}
