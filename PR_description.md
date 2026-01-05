# Security By Design - Zadanie 4

## Zadanie 1 (opcjonalne): Trivy na lokalnie zbudowanym obrazie Dockera

W ramach zadania zbudowano lokalnie obraz Dockerowy aplikacji na podstawie dostarczonego pliku Dockerfile.
Następnie przeprowadzono skanowanie bezpieczeństwa za pomocą narzędzia Trivy, weryfikując warstwy obrazu pod kątem
znanych podatności (CVE). Wyniki analizy ujawniły obecność licznych luk o krytycznym i wysokim poziomie ryzyka,
co wskazuje na konieczność aktualizacji obrazu bazowego przed wdrożeniem.

Wyniki skanowania:
```
Report Summary

┌───────────────────────────────┬────────┬─────────────────┬─────────┐
│            Target             │  Type  │ Vulnerabilities │ Secrets │
├───────────────────────────────┼────────┼─────────────────┼─────────┤
│ tbo-task4-local (debian 13.0) │ debian │       70        │    -    │
├───────────────────────────────┼────────┼─────────────────┼─────────┤
│ app.jar                       │  jar   │       72        │    -    │
└───────────────────────────────┴────────┴─────────────────┴─────────┘
Legend:
- '-': Not scanned
- '0': Clean (no security findings detected)


tbo-task4-local (debian 13.0)
=============================
Total: 70 (UNKNOWN: 0, LOW: 53, MEDIUM: 16, HIGH: 0, CRITICAL: 1)
```

## Zadanie 2 (opcjonalne): SAST z wykorzystaniem Semgrep

W ramach zadania przeprowadzono statyczną analizę kodu źródłowego aplikacji przy użyciu narzędzia Semgrep.

Wyniki analizy ujawniły kilka potencjalnych problemów bezpieczeństwa, w tym:
```
┌─────────────┐
│ Scan Status │
└─────────────┘
  Scanning 1798 files tracked by git with 1063 Code rules:
                                                                                                                        
  Language      Rules   Files          Origin      Rules                                                                
 ─────────────────────────────        ───────────────────                                                               
  <multilang>      62    1798          Community    1063                                                                
  js              156      13                                                                                           
  java            118      13                                                                                           
  html              1       8                                                                                           
  yaml             31       6                                                                                           
  json              4       6                                                                                           
  dockerfile        6       2                                                                                           
  ruby             72       1                                                                                           
                                                                                                                        
                    
                    
┌──────────────────┐
│ 25 Code Findings │
└──────────────────┘
              
    Dockerfile
   ❯❯❱ dockerfile.security.missing-user-entrypoint.missing-user-entrypoint
          By not specifying a USER, a program in the container may run as 'root'. This is a security hazard.
          If an attacker can control a process running as root, they may have control over the container.   
          Ensure that the last USER in a Dockerfile is a USER other than 'root'.                            
          Details: https://sg.run/k281                                                                      
                                                                                                            
           ▶▶┆ Autofix ▶ USER non-root ENTRYPOINT ["java", "-jar", "/app.jar"]
           22┆ ENTRYPOINT ["java", "-jar", "/app.jar"]
                                                                      
    src/main/resources/static/vendors/bootstrap/js/bootstrap.bundle.js
    ❯❱ javascript.lang.security.audit.detect-non-literal-regexp.detect-non-literal-regexp
          RegExp() called with a `configTypes` function argument, this might allow an attacker to cause a     
          Regular Expression Denial-of-Service (ReDoS) within your application as RegExP blocks the main      
          thread. For this reason, it is recommended to use hardcoded regexes instead. If your regex is run on
          user-controlled input, consider performing input validation or use a regex checking/sanitization    
          library such as https://www.npmjs.com/package/recheck to verify that the regex does not appear      
          vulnerable to ReDoS.                                                                                
          Details: https://sg.run/gr65                                                                        
                                                                                                              
          213┆ if (!new RegExp(expectedTypes).test(valueType)) {
            ⋮┆----------------------------------------
    ❯❱ javascript.lang.security.audit.detect-non-literal-regexp.detect-non-literal-regexp
          RegExp() called with a `property` function argument, this might allow an attacker to cause a Regular
          Expression Denial-of-Service (ReDoS) within your application as RegExP blocks the main thread. For  
          this reason, it is recommended to use hardcoded regexes instead. If your regex is run on user-      
          controlled input, consider performing input validation or use a regex checking/sanitization library 
          such as https://www.npmjs.com/package/recheck to verify that the regex does not appear vulnerable to
          ReDoS.                                                                                              
          Details: https://sg.run/gr65                                                                        
                                                                                                              
          213┆ if (!new RegExp(expectedTypes).test(valueType)) {
   
   ❯❯❱ javascript.browser.security.insecure-document-method.insecure-document-method
          User controlled data in methods like `innerHTML`, `outerHTML` or `document.write` is an anti-pattern
          that can lead to XSS vulnerabilities                                                                
          Details: https://sg.run/LwA9                                                                        
                                                                                                              
          5547┆ element.innerHTML = this._config.template;
            ⋮┆----------------------------------------
          5583┆ element.innerHTML = content;
                                                                   
    src/main/resources/static/vendors/bootstrap/js/bootstrap.esm.js
    ❯❱ javascript.lang.security.audit.detect-non-literal-regexp.detect-non-literal-regexp
          RegExp() called with a `configTypes` function argument, this might allow an attacker to cause a     
          Regular Expression Denial-of-Service (ReDoS) within your application as RegExP blocks the main      
          thread. For this reason, it is recommended to use hardcoded regexes instead. If your regex is run on
          user-controlled input, consider performing input validation or use a regex checking/sanitization    
          library such as https://www.npmjs.com/package/recheck to verify that the regex does not appear      
          vulnerable to ReDoS.                                                                                
          Details: https://sg.run/gr65                                                                        
                                                                                                              
          209┆ if (!new RegExp(expectedTypes).test(valueType)) {
            ⋮┆----------------------------------------
    ❯❱ javascript.lang.security.audit.detect-non-literal-regexp.detect-non-literal-regexp
          RegExp() called with a `property` function argument, this might allow an attacker to cause a Regular
          Expression Denial-of-Service (ReDoS) within your application as RegExP blocks the main thread. For  
          this reason, it is recommended to use hardcoded regexes instead. If your regex is run on user-      
          controlled input, consider performing input validation or use a regex checking/sanitization library 
          such as https://www.npmjs.com/package/recheck to verify that the regex does not appear vulnerable to
          ReDoS.                                                                                              
          Details: https://sg.run/gr65                                                                        
                                                                                                              
          209┆ if (!new RegExp(expectedTypes).test(valueType)) {
   
   ❯❯❱ javascript.browser.security.insecure-document-method.insecure-document-method
          User controlled data in methods like `innerHTML`, `outerHTML` or `document.write` is an anti-pattern
          that can lead to XSS vulnerabilities                                                                
          Details: https://sg.run/LwA9                                                                        
                                                                                                              
          3789┆ element.innerHTML = this._config.template;
            ⋮┆----------------------------------------
          3825┆ element.innerHTML = content;
                                                               
    src/main/resources/static/vendors/bootstrap/js/bootstrap.js
    ❯❱ javascript.lang.security.audit.detect-non-literal-regexp.detect-non-literal-regexp
          RegExp() called with a `configTypes` function argument, this might allow an attacker to cause a     
          Regular Expression Denial-of-Service (ReDoS) within your application as RegExP blocks the main      
          thread. For this reason, it is recommended to use hardcoded regexes instead. If your regex is run on
          user-controlled input, consider performing input validation or use a regex checking/sanitization    
          library such as https://www.npmjs.com/package/recheck to verify that the regex does not appear      
          vulnerable to ReDoS.                                                                                
          Details: https://sg.run/gr65                                                                        
                                                                                                              
          235┆ if (!new RegExp(expectedTypes).test(valueType)) {
            ⋮┆----------------------------------------
    ❯❱ javascript.lang.security.audit.detect-non-literal-regexp.detect-non-literal-regexp
          RegExp() called with a `property` function argument, this might allow an attacker to cause a Regular
          Expression Denial-of-Service (ReDoS) within your application as RegExP blocks the main thread. For  
          this reason, it is recommended to use hardcoded regexes instead. If your regex is run on user-      
          controlled input, consider performing input validation or use a regex checking/sanitization library 
          such as https://www.npmjs.com/package/recheck to verify that the regex does not appear vulnerable to
          ReDoS.                                                                                              
          Details: https://sg.run/gr65                                                                        
                                                                                                              
          235┆ if (!new RegExp(expectedTypes).test(valueType)) {
   
   ❯❯❱ javascript.browser.security.insecure-document-method.insecure-document-method
          User controlled data in methods like `innerHTML`, `outerHTML` or `document.write` is an anti-pattern
          that can lead to XSS vulnerabilities                                                                
          Details: https://sg.run/LwA9                                                                        
                                                                                                              
          3815┆ element.innerHTML = this._config.template;
            ⋮┆----------------------------------------
          3851┆ element.innerHTML = content;
                                                                   
    src/main/resources/static/vendors/fontawesome/js/fontawesome.js
   ❯❯❱ javascript.browser.security.insecure-document-method.insecure-document-method
          User controlled data in methods like `innerHTML`, `outerHTML` or `document.write` is an anti-pattern
          that can lead to XSS vulnerabilities                                                                
          Details: https://sg.run/LwA9                                                                        
                                                                                                              
          630┆ style.innerHTML = css;
            ⋮┆----------------------------------------
          1360┆ node.outerHTML = newOuterHTML + (config.keepOriginalSource && node.tagName.toLowerCase()
               !== 'svg' ? "<!-- ".concat(node.outerHTML, " Font Awesome fontawesome.com -->") : '');   
            ⋮┆----------------------------------------
          1364┆ newNode.outerHTML = newOuterHTML;
            ⋮┆----------------------------------------
          1397┆ node.innerHTML = newInnerHTML;
            ⋮┆----------------------------------------
          2056┆ element.outerHTML = abstract.map(function (a) {
          2057┆   return toHtml(a);
          2058┆ }).join('\n');
            ⋮┆----------------------------------------
          2190┆ container.innerHTML = val.html;
                                                            
    src/main/resources/static/vendors/jquery-mask/Dockerfile
   ❯❯❱ dockerfile.security.missing-user.missing-user
          By not specifying a USER, a program in the container may run as 'root'. This is a security hazard.
          If an attacker can control a process running as root, they may have control over the container.   
          Ensure that the last USER in a Dockerfile is a USER other than 'root'.                            
          Details: https://sg.run/Gbvn                                                                      
                                                                                                            
           ▶▶┆ Autofix ▶ USER non-root CMD ["/sbin/my_init"]
            4┆ CMD ["/sbin/my_init"]
   
    ❯❱ dockerfile.security.no-sudo-in-dockerfile.no-sudo-in-dockerfile
          Avoid using sudo in Dockerfiles. Running processes as a non-root user can help  reduce the potential
          impact of configuration errors and security vulnerabilities.                                        
          Details: https://sg.run/80Q7                                                                        
                                                                                                              
           32┆ RUN curl --silent --location https://deb.nodesource.com/setup_0.12 | sudo bash -
                                                           
    src/main/resources/static/vendors/jquery-mask/deploy.rb
    ❯❱ ruby.lang.security.dangerous-subshell.dangerous-subshell
          Detected non-static command inside `...`. If unverified user data can reach this call site, this is 
          a code injection vulnerability. A malicious actor can inject a malicious script to execute arbitrary
          code.                                                                                               
          Details: https://sg.run/NrxL                                                                        
                                                                                                              
           44┆ `git commit -am 'generating jquery mask files #{JMASK_VERSION}'`
                                                                    
    src/main/resources/static/vendors/jquery-mask/src/jquery.mask.js
    ❯❱ javascript.lang.security.audit.detect-non-literal-regexp.detect-non-literal-regexp
          RegExp() called with a `mask` function argument, this might allow an attacker to cause a Regular    
          Expression Denial-of-Service (ReDoS) within your application as RegExP blocks the main thread. For  
          this reason, it is recommended to use hardcoded regexes instead. If your regex is run on user-      
          controlled input, consider performing input validation or use a regex checking/sanitization library 
          such as https://www.npmjs.com/package/recheck to verify that the regex does not appear vulnerable to
          ReDoS.                                                                                              
          Details: https://sg.run/gr65                                                                        
                                                                                                              
          165┆ r = r.replace(new RegExp('(' + oRecursive.digit + '(.*' + oRecursive.digit + ')?)'),
               '($1)?')                                                                            
            ⋮┆----------------------------------------
          166┆ .replace(new RegExp(oRecursive.digit, 'g'), oRecursive.pattern);
            ⋮┆----------------------------------------
          169┆ return new RegExp(r);

                
                
┌──────────────┐
│ Scan Summary │
└──────────────┘
✅ Scan completed successfully.
 • Findings: 25 (25 blocking)
 • Rules run: 445
 • Targets scanned: 1798
 • Parsed lines: ~100.0%
 • Scan skipped: 
   ◦ Files larger than  files 1.0 MB: 2
   ◦ Files matching .semgrepignore patterns: 30
 • Scan was limited to files tracked by git
 • For a detailed list of skipped files and lines, run semgrep with the --verbose flag
Ran 445 rules on 1798 files: 25 findings.
```


## Zadanie 3 (obowiązkowe): Przygotowanie procesu CI/CD z wykorzystaniem Trivy i Semgrep

W ramach zadania proces ciągłej integracji (CI) w oparciu o GitHub Actions, definiując workflow w pliku
`.github/workflows/main.yml`. Potok automatycznie buduje obraz Dockera oraz uruchamia skanowanie kodu źródłowego
narzędziem Semgrep i analizę obrazu kontenera narzędziem Trivy przy każdym wypchnięciu zmian do repozytorium.
Konfiguracja ta umożliwia zaimplementowanie w przyszłosći automatycznego blokowania wdrożenia w przypadku wykrycia
podatności o poziomie krytycznym lub wysokim (Critical/High).

Link do joba z wykonanym pipelinem dla przykładowego commita:
https://github.com/pawelmuller/TBO-task4/actions/runs/20730093571


## Zadanie 4 (obowiązkowe): Uruchomienie aplikacji lokalnie + DAST z wykorzystaniem ZAP