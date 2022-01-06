# Lab3_1-4_Jenkinsfile
Jenkinsfile calling Shared Library Function outputting build start time and end time



## curl command kicking off job

curl -v -X POST -H "Content-Type:application/json" -H 'x-hub-signature:0e504c83eef6caa6a363c2841432a20898de03b5485dcb36dce02c73eb855799' \
-d '{"fact": { "id": "https://github.com/dylanmeh/Lab3_1-4_Jenkinsfile" }}' \
http://a00562d6411a94462849380fe77fba39-1268481901.us-east-1.elb.amazonaws.com/cjoc/webhooks/sh3WUME/

