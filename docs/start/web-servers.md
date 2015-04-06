# Apache 配置

请确保 `.htaccess` 和 `index.php` 这两个文件位于同一个可公开访问的目录中。`.htaccess` 文件包含以下代码：

    RewriteEngine On
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^ index.php [QSA,L]

此外，为了保证 `.htaccess` 文件中的重写规则（rewrite rules）能够生效，虚拟服务器必须启用 `AllowOverride` 选项：

    AllowOverride All

# Nginx 配置

假设 Slim 的 `index.php` 文件位于项目的根目录（www root），将以下代码和其他需要的设置一起写入到 nginx 配置文件的 `location` 块中：

    try_files $uri $uri/ /index.php?$args;

# HHVM

将以下设置代码中的 `SourceRoot` 修改为 Slim 应用的文档根目录，然后和其他需要的设置一起写入到 HHVM 配置文件中：

    Server {
        SourceRoot = /path/to/public/directory
    }

    ServerVariables {
        SCRIPT_NAME = /index.php
    }

    VirtualHost {
        * {
            Pattern = .*
            RewriteRules {
                    * {
                            pattern = ^(.*)$
                            to = index.php/$1
                            qsa = true
                    }
            }
        }
    }

# IIS

请确保 `Web.config` 和 `index.php` 这两个文件位于同一个可公开访问的目录中。`Web.config` 文件包含以下代码：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <rewrite>
            <rules>
                <rule name="slim" patternSyntax="Wildcard">
                    <match url="*" />
                    <conditions>
                        <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
                        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
                    </conditions>
                    <action type="Rewrite" url="index.php" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>
</configuration>
```

# lighttpd

假设 Slim 的 `index.php` 文件位于项目的根目录（www root），将以下代码和其他需要的设置一起写入到 lighttpd（>= 1.4.24）配置文件中：

    url.rewrite-if-not-file = ("(.*)" => "/index.php/$0")
