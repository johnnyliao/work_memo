# git-runner註冊時遇到x509: certificate signed by unknown authority

執行git-runner register gitlab時，遇到錯誤訊息

    Couldn't execute POST against https://hostname.tld/api/v4/jobs/request: Post。
    https://hostname.tld/api/v4/jobs/request: x509: certificate signed by unknown authority
是因為gitlab container使用自簽評證造成git-runner無法順利連線，需自行將crt匯入git-runner內就可解決

 1. 進入git-runner container內
 
    `sudo docker exec -it git-runner bash`

 2. 利用openssl下載gitlab crt憑證
  

    `openssl s_client -showcerts  -connect gitlab_url:443 < /dev/null 2>/dev/null | openssl x509 -outform PEM > /etc/gitlab-runner/gitlab.crt`

 3. 驗證crt正確性
 

    `echo | openssl s_client -CAfile /etc/gitlab-runner/gitlab.crt -connect gitlab_url:443`

 4. 代入crt路徑重新register
      `git-runner register --tls-ca-file /etc/gitlab-runner/gitlab.crt`
 
