# arachni            
> Arachni - A Web Application Security Scanner Framework
>
> 官网: [https://www.arachni-scanner.com](https://www.arachni-scanner.com) | 文档: [https://www.arachni-scanner.com/wiki](https://www.arachni-scanner.com/wiki)
>
> **注意**：Arachni 项目已由 Codename SCNR 接替，新项目推荐使用 [Codename SCNR](https://documentation.ecsypno.com/scnr/)。

## 0x01 使用

```
arachni -h                   
Arachni - Web Application Security Scanner Framework v1.5.1
   Author: Tasos "Zapotek" Laskos <tasos.laskos@arachni-scanner.com>

           (With the support of the community and the Arachni Team.)

   Website:       http://arachni-scanner.com
   Documentation: http://arachni-scanner.com/wiki


Usage: ./arachni [options] URL

Generic
  -h, --help                  Output this message.
                               
      --version               Show version information.
                               
      --daemon-friendly       Enable this option when running the process in the background.
                               
      --authorized-by EMAIL_ADDRESS
                              E-mail address of the person who authorized the scan.
                                (It'll make it easier on the sys-admins during log reviews.)
                                (Will be used as a value for the 'From' HTTP request header.)
                               

Output
      --output-verbose        Show verbose output.
                               
      --output-debug [LEVEL 1-4]
                              Show debugging information.
                               
      --output-only-positives Only output positive results.
                               

Scope
      --scope-include-pattern PATTERN
                              Only include resources whose path/action matches PATTERN.
                                (Can be used multiple times.)
                               
      --scope-include-subdomains
                              Follow links to subdomains.
                                (Default: false)
                               
      --scope-exclude-pattern PATTERN
                              Exclude resources whose path/action matches PATTERN.
                                (Can be used multiple times.)
                               
      --scope-exclude-file-extensions EXTENSION,EXTENSION2,..
                              Exclude resources with the specified extensions.
                               
      --scope-exclude-content-pattern PATTERN
                              Exclude pages whose content matches PATTERN.
                                (Can be used multiple times.)
                               
      --scope-exclude-binaries
                              Exclude non text-based pages.
                                (Binary content can confuse passive checks that perform pattern matching.)
                               
      --scope-redundant-path-pattern PATTERN:LIMIT
                              Limit crawl on redundant pages like galleries or catalogs.
                                (URLs matching PATTERN will be crawled LIMIT amount of times.)
                                (Can be used multiple times.)
                               
      --scope-auto-redundant [LIMIT]
                              Only follow URLs with identical query parameter names LIMIT amount of times.
                                (Default: 10)
                               
      --scope-directory-depth-limit LIMIT
                              Directory depth limit.
                                (Default: inf)
                                (How deep Arachni should go into the site structure.)
                               
      --scope-page-limit LIMIT
                              How many pages to crawl and audit.
                                (Default: inf)
                               
      --scope-extend-paths FILE
                              Add the paths in FILE to the ones discovered by the crawler.
                                (Can be used multiple times.)
                               
      --scope-restrict-paths FILE
                              Use the paths in FILE instead of crawling.
                                (Can be used multiple times.)
                               
      --scope-url-rewrite PATTERN:SUBSTITUTION
                              Rewrite URLs based on the given PATTERN and SUBSTITUTION.
                                To convert:  http://example.com/articles/some-stuff/23 to http://example.com/articles.php?id=23
                                Use:         articles/[\w-]+/(\d+):articles.php?id=\1
                               
      --scope-dom-depth-limit LIMIT
                              How deep to go into the DOM tree of each page, for pages with JavaScript code.
                                (Default: 5)
                                (Setting it to '0' will disable browser analysis.)
                               
      --scope-dom-event-limit LIMIT
                              How many DOM events to trigger for each DOM depth, for pages with JavaScript code.
                                (Default: inf)
                               
      --scope-https-only      Forces the system to only follow HTTPS URLs.
                                (Default: false)
                               

Audit
      --audit-links           Audit links.
                               
      --audit-forms           Audit forms.
                               
      --audit-cookies         Audit cookies.
                               
      --audit-cookies-extensively
                              Submit all links and forms of the page along with the cookie permutations.
                                (*WARNING*: This will severely increase the scan-time.)
                               
      --audit-headers         Audit headers.
                               
      --audit-link-template TEMPLATE
                              Regular expression with named captures to use to extract input information from generic paths.
                                To extract the 'input1' and 'input2' inputs from:
                                  http://example.com/input1/value1/input2/value2
                                Use:
                                  input1/(?<input1>\w+)/input2/(?<input2>\w+)
                                (Can be used multiple times.)
                               
      --audit-jsons           Audit JSON request inputs.
                               
      --audit-xmls            Audit XML request inputs.
                               
      --audit-ui-inputs       Audit orphan <input> elements with events.
                               
      --audit-ui-forms        Audit UI Forms.
                                Input and button groups that do not belong to a parent <form> element.
                               
      --audit-parameter-names Inject payloads into parameter names.
                               
      --audit-with-raw-payloads
                              Inject payloads with and without HTTP encoding.
                               
      --audit-with-extra-parameter
                              Inject payloads into extra element parameters.
                               
      --audit-with-both-methods
                              Audit elements with both GET and POST requests.
                                (*WARNING*: This will severely increase the scan-time.)
                               
      --audit-exclude-vector PATTERN
                              Exclude input vectors whose name matches PATTERN.
                                (Can be used multiple times.)
                               
      --audit-include-vector PATTERN
                              Include only input vectors whose name matches PATTERN.
                                (Can be used multiple times.)
                               

Input
      --input-value PATTERN:VALUE
                              PATTERN to match against input names and VALUE to use for them.
                                (Can be used multiple times.)
                               
      --input-values-file FILE
                              YAML file containing a Hash object with regular expressions, to match against input names, as keys and input values as values.
                               
      --input-without-defaults
                              Do not use the system default input values.
                               
      --input-force           Fill-in even non-empty inputs.
                               

HTTP
      --http-user-agent USER_AGENT
                              Value for the 'User-Agent' HTTP request header.
                                (Default: Arachni/v1.5.1)
                               
      --http-request-concurrency MAX_CONCURRENCY
                              Maximum HTTP request concurrency.
                                (Default: 20)
                                (Be careful not to kill your server.)
                                (*NOTE*: If your scan seems unresponsive try lowering the limit.)
                               
      --http-request-timeout TIMEOUT
                              HTTP request timeout in milliseconds.
                                (Default: 10000)
                               
      --http-request-redirect-limit LIMIT
                              Maximum amount of redirects to follow for each HTTP request.
                                (Default: 5)
                               
      --http-request-queue-size QUEUE_SIZE
                              Maximum amount of requests to keep in the queue.
                                Bigger size means better scheduling and better performance,
                                smaller means less RAM consumption.
                                (Default: 100)
                               
      --http-request-header NAME=VALUE
                              Specify custom headers to be included in the HTTP requests.
                                (Can be used multiple times.)
                               
      --http-response-max-size LIMIT
                              Do not download response bodies larger than the specified LIMIT, in bytes.
                                (Default: 500000)
                               
      --http-cookie-jar COOKIE_JAR_FILE
                              Netscape-styled HTTP cookiejar file.
                               
      --http-cookie-string COOKIE
                              Cookie representation as a 'Set-Cookie' HTTP response header.
                                Example: my_cookie=my_value; Path=/, other_cookie=other_value; Path=/test
                               
      --http-authentication-username USERNAME
                              Username for HTTP authentication.
                               
      --http-authentication-password PASSWORD
                              Password for HTTP authentication.
                               
      --http-authentication-type auto,basic,digest,digest_ie,negotiate,ntlm
                              HTTP authentication type.
                                (Default: auto)
                               
      --http-proxy ADDRESS:PORT
                              Proxy to use.
                               
      --http-proxy-authentication USERNAME:PASSWORD
                              Proxy authentication credentials.
                               
      --http-proxy-type http,http_1_0,socks4,socks4a,socks5,socks5h
                              Proxy type.
                                (Default: auto)
                               
      --http-ssl-verify-peer  Verify SSL peer.
                                (Default: false)
                               
      --http-ssl-verify-host  Verify SSL host.
                                (Default: false)
                               
      --http-ssl-certificate PATH
                              SSL certificate to use.
                               
      --http-ssl-certificate-type pem,der
                              SSL certificate type.
                               
      --http-ssl-key PATH     SSL private key to use.
                               
      --http-ssl-key-type pem,der
                              SSL key type.
                               
      --http-ssl-key-password PASSWORD
                              Password for the SSL private key.
                               
      --http-ssl-ca PATH      File holding one or more certificates with which to verify the peer.
                               
      --http-ssl-ca-directory PATH
                              Directory holding multiple certificate files with which to verify the peer.
                               
      --http-ssl-version TLSv1,TLSv1_0,TLSv1_1,TLSv1_2,SSLv2,SSLv3
                              SSL version to use.
                               

Checks
      --checks-list [GLOB]    List available checks based on the provided glob.
                                (If no glob is provided all checks will be listed.)
                               
      --checks CHECK,CHECK2,...
                              Comma separated list of checks to load.
                                    Checks are referenced by their filename without the '.rb' extension, use '--checks-list' to list all.
                                    Use '*' as a check name to load all checks or as a wildcard, like so:
                                        xss*   to load all XSS checks
                                        sql_injection*  to load all SQL injection checks
                                        etc.
                                
                                    You can exclude checks by prefixing their name with a minus sign:
                                        --checks=*,-backup_files,-xss
                                    The above will load all checks except for the 'backup_files' and 'xss' checks.
                                
                                    Or mix and match:
                                        -xss*   to unload all XSS checks.
                               

Plugins
      --plugins-list [GLOB]   List available plugins based on the provided glob.
                                (If no glob is provided all plugins will be listed.)
                               
      --plugin 'PLUGIN:OPTION=VALUE,OPTION2=VALUE2'
                              PLUGIN is the name of the plugin as displayed by '--plugins-list'.
                                (Plugins are referenced by their filename without the '.rb' extension, use '--plugins-list' to list all.)
                                (Can be used multiple times.)
                               

Platforms
      --platforms-list        List available platforms.
                               
      --platforms-no-fingerprinting
                              Disable platform fingerprinting.
                                (By default, the system will try to identify the deployed server-side platforms automatically
                                in order to avoid sending irrelevant payloads.)
                               
      --platforms PLATFORM,PLATFORM2,...
                              Comma separated list of platforms (by shortname) to audit.
                                (The given platforms will be used *in addition* to fingerprinting. In order to restrict the audit to
                                these platforms enable the '--platforms-no-fingerprinting' option.)
                               

Session
      --session-check-url URL URL to use to verify that the scanner is still logged in to the web application.
                                (Requires 'session-check-pattern'.)
                               
      --session-check-pattern PATTERN
                              Pattern used against the body of the 'session-check-url' to verify that the scanner is still logged in to the web application.
                                (Requires 'session-check-url'.)
                               

Profiles
      --profile-save-filepath FILEPATH
                              Save the current configuration profile/options to FILEPATH.
                               
      --profile-load-filepath FILEPATH
                              Load a configuration profile from FILEPATH.
                               

Browser cluster
      --browser-cluster-local-storage FILE
                              Sets the browsers' local storage using the JSON data in FILE.
                               
      --browser-cluster-wait-for-element PATTERN:CSS
                              Wait for element matching CSS to appear when visiting a page whose URL matches the PATTERN.
                               
      --browser-cluster-pool-size SIZE
                              Amount of browser workers to keep in the pool and put to work.
                                (Default: 6)
                               
      --browser-cluster-job-timeout SECONDS
                              Maximum allowed time for each job.
                                (Default: 10)
                               
      --browser-cluster-worker-time-to-live LIMIT
                              Re-spawn the browser of each worker every LIMIT jobs.
                                (Default: 100)
                               
      --browser-cluster-ignore-images
                              Do not load images.
                               
      --browser-cluster-screen-width
                              Browser screen width.
                                (Default: 1600)
                               
      --browser-cluster-screen-height
                              Browser screen height.
                                (Default: 1200)
                               

Report
      --report-save-path PATH Directory or file path where to store the scan report.
                                You can use the generated file to create reports in several formats with the 'arachni_reporter' executable.
                               

Snapshot
      --snapshot-save-path PATH
                              Directory or file path where to store the snapshot of a suspended scan.
                                You can use the generated file to resume the scan with the 'arachni_restore' executable.
                               

Timeout
      --timeout HOURS:MINUTES:SECONDS
                              Stop the scan after the given duration is exceeded.
                               
      --timeout-suspend       Suspend after the timeout.
                                You can use the generated file to resume the scan with the 'arachni_restore' executable.
```

实例: `arachni https://baidu.com`
