/*
 * Copyright 2013-2016 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

node {

	stage 'Checkout'
		checkout scm


	stage 'Build'
		sh "./mvnw clean org.jacoco:jacoco-maven-plugin:prepare-agent install -Psonar"
		archive includes: '*.jar', excludes: '*-sources.jar'
		//step([$class: 'JUnitResultArchiver', testResults: '**/target/surefire-reports/TEST-*.xml'])
		stash excludes: 'target/', includes: '**', name: 'source'

	parallel([
			'Sonar': {
				node {
					unstash 'source'
					sh './mvnw sonar:sonar -Psonar -Dsonar.host.url=https://sonar.spring.io'
				}
			},
			'E2E tests'   : {
				node {
					unstash 'source'
					//sh 'sh -e scripts/runAcceptanceTests.sh'
					echo 'Not running E2E cause docker-compose is not installed on slaves'
					archive includes: '*.jar', excludes: '*-sources.jar'
					//step([$class: 'JUnitResultArchiver', testResults: '**/test-results/*.xml'])
				}
			}
	])
}