# GHSA-rrrc-p5v6-mvxf
GHSA-rrrc-p5v6-mvxf phpsysinfo cmd injection via locale config file


A command Injection vulnerability exists in phpSysInfo within read_config.php. The application dynamically reads system locale configuration files (such as /etc/default/locale or /etc/locale.conf) and parses them using a regular expression (/^(LANG="?[^"\n]*"?)/m).

The captured string array is subsequently passed directly into an exec() sink without input sanitization or argument separation:
@exec($matches[1].' locale -k LC_CTYPE 2>/dev/null', $lines)

If a local attacker can manipulate or inject into these environment file structures, or if the application is deployed in a context where configuration files are write-accessible to non-root processes, an attacker can break out of the string context using shell execution delimiters (e.g., ;, &&, or |) to execute arbitrary host shell commands under the permissions of the web server user (www-data). 

https://github.com/phpsysinfo/phpsysinfo/security/advisories/GHSA-rrrc-p5v6-mvxf

- b1nv0id


poc

    Create or modify a configuration file tracked by read_config.php (such as a simulated layout file in a nested environment partition).
    Inject a command payload into the LANG field:
    LANG="; touch /tmp/vulnerable_sysinfo; #"
    Access or trigger phpsysinfo/index.php.


    Verify that /tmp/vulnerable_sysinfo is created under the web server's execution context.

