job('task6job1') {
	    description("This job will pull the github repository when developer commit code, create or update the container using given Dockerfile and push image to DockerHub")
	    scm {
	        github('kethavathsivanaik/task6','master')
	    }
	    triggers {
	    scm("* * * * *")
	  }
	   steps {
	    shell("cp -rvf * /root")
	  }
	
}
job("task6job2") {
	  triggers {
	    upstream('task6job1','SUCCESS')
	  }
	  steps {
	    shell('''if [[ -f /root/index.html ]]
	    then
	        echo "index.html file exists so running Webserver to deploy."
	        kubectl apply -f /root/deploy-html.yml
	    fi
	    if [[ -f /root/index.php ]]
	    then
	        echo "index.php file exists so running Webserver with PHP to deploy."
	        kubectl apply -f /root/deploy-php.yml
	    fi
	    if [[ -f /root/sample.war ]]
	    then
	        echo "sample.war file exists so running TomCat to deploy."
	        kubectl apply -f /root/deploy-tom.yml
	fi''')
	  }
	
}
job("task6job3job4") {
	  description ("This Job will Test and Notify Developer")
	  triggers {
	    upstream('task6job2','SUCCESS')   
	  }
	steps {
	  shell('''ip=$(curl ifconfig.me)
	if kubectl get deployment | grep html-deploy
	then
	    echo "Testing deployment in html-docker..."
	    port=$(kubectl get svc html-deploy -o=yaml -o jsonpath='{.spec.ports[*].nodePort}')
	    status_html=$(curl  -o /dev/null  -s  -w  "%{http_code}"  http://$ip:$port/index.html)
	    if [ $status_html==200 ]; then
	    	echo "website is working"
	    else
	    	echo "website not working"
	    	exit 1
		fi
	fi
	if kubectl get deployment | grep php-deploy
	then
	     echo "Testing deployment in php-docker..."
	     port=$(kubectl get svc php-deploy -o=yaml -o jsonpath='{.spec.ports[*].nodePort}')
	    status_php=$(curl  -o /dev/null  -s  -w  "%{http_code}"  http://$ip:$port/index.php)
	    if [ $status_php==200 ]; then
	    	echo "website is working"
	    else
	    	echo "website not working"
	    	exit 1
		fi
	fi
	if kubectl get deployment | grep tomcat-deploy
	then
	     echo "Testing deployment in tomcat-docker"
	     port=$(kubectl get svc tom-deploy -o=yaml -o jsonpath='{.spec.ports[*].nodePort}')
	    status_tom=$(curl  -o /dev/null  -s  -w  "%{http_code}"  http://$ip:$port/sample/index.html)
	    if [ $status_tom==200 ]; then
	    	echo "website is working"
	    else
	    	echo "website not working"
	    	exit 1
		fi
	fi''')
	}
	    configure { project ->
	    project / publishers << 'jenkins.plugins.slack.SlackNotifier' {
	    room("U011G9GPPCM")
	    notifyAborted(false)
	    notifyFailure(true)
	    notifyNotBuilt(false)
	    notifyUnstable(false)
	    notifyBackToNormal(false)
	    notifySuccess(true)
	    notifyRepeatedFailure(false)
	    startNotification(true)
	    includeTestSummary(true)
	    includeCustomMessage(false)
	    sendAs(null)
	    tokenCredentialId('slack_token')
	    commitInfoChoice("NONE")
	    teamDomain("linuxworldindia")
	   }
	  }
	
}
