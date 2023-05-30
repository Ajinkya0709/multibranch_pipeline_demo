// This template was taken from: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/workflows/githubPr/#example

@Library("ccif@v0.5.0") _ // load the CCIF shared library, select a version via @Library("ccif@<tag|branch|commit>") _

// Usage and content of this map is completely up to the project
Map projectConfig = [
    sshPrivate: 'github-vni.geo.conti.de-sshPrivate-CCIF',
    cloneUrl  : 'git@github-vni.geo.conti.de:bs-vw-meb-19-icas1-chorus/sw.sys.chorus_main_build.git',
    variants  : ['icasevo'],
]

// Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/workflows/githubPr/#config
Map workflowConfig = [
    github: [
        // token of service account used for GitHub REST API communication
        authConfig: 'github-vni.geo.conti.de-token-CCIF'
    ],

    // Artifactory upload configuration. Remove, if you don't need artifact storage.
    // Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/workflows/artifacts/#artifactory-upload-config
    /*
	artifactory: [
        repoRootPath     : 'vni_pmt_ccif_generic_l/demo/sw.sys.demo-prj-win-mono',
        server           : 'https://eu.artifactory.conti.de/artifactory',
        authConfig       : 'artifactory.geo.conti.de-token',
        artifactsDir     : 'artifacts',
    ],
	*/

    // Upload to reporting database - adapt and uncomment to enable temporary solution
    // Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/reference/reporting/#upload
    /*
    reporting   : [
            mode       : 'viaJenkinsJob',   // temporary solution
            projectName: 'test',
            credential : 'jenkins-ccif-infrastructure.cmo.conti.de-token'
    ],
    */

    // merge configuration for MegaMerge - uncomment, if you use MegaMerge
    /*
    merge: [
        strategy: 'megaMerge'
    ],
    */

    // Jira ticket validation configuration - uncomment to enable
    // Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/reference/jira/workflow/#configuration
    jira: [
        workflows: [
            [
                name       : 'VWICAS23',
                serverUrl  : 'https://jira-ibs.zone2.agileci.conti.de',
                auth       : "FunctionalAccount",
                projects   : ['VWICAS23'],
                verifyQuery: "Status in ('In Progress')",
                shouldCommentError : true,
                //transition : 'Requires PO Approval',
            ]
        ]
    ],
]

// Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/reference/logger/#overview
env.LOG_LEVEL = 'ALL'

// Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/reference/
ccif.init()

timestamps { // enable timestamps in console output
    node('ICAS_BuildTest') {

        // Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/workflows/githubPr/
        workflow.githubPr(workflowConfig) { Map ccifParams ->

            // Parallel checkout of all project variants into separate sub folders
            // Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/reference/gate/#equaltasks
            gate.equalTasks('checkout', projectConfig.variants) { String variantName, Map variantSummaryMap, Map variantDbMap ->

                // Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/reference/errors/#exclusivewrap
                errors.exclusiveWrap("${variantName}: Unable to checkout PR") {

                    // Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/reference/checkouts/#monorepository
                    String checkoutSha = checkouts.monoRepository([
                        remoteRepo: projectConfig.cloneUrl,
                        credential: projectConfig.sshPrivate,
                        //targetDir : variantName,
                        pullRequestNumber: ccifParams.prInfo.number
                    ])

                    variantSummaryMap << [sha:checkoutSha] // add checkout sha to summary
                    variantDbMap      << [sha:checkoutSha] // add checkout sha to reporting database
                }
            }
            //#################################################################
            gate.equalTasks('build', projectConfig.variants) { String variantName, Map variantSummaryMap, Map variantDbMap ->

                // Doc: https://github.geo.conti.de/pages/CCIF/jenkins-shared-library/master/reference/errors/#exclusivewrap
                errors.exclusiveWrap("${variantName}: Unable to build PR") {
		
					//dir (variantName) {
                        strPayload="OVERWRITTEN BY JOB"
						withEnv(["PATH=/d/CICD_Tools/python/v3.8.5:$PATH", 'Cips_BuildType=pull-request', 'payload="OVERWRITTEN BY JOB"']) {
                            println ">>>> Variant to be built: "+variantName
                            powershell ("Get-ChildItem Env: | Sort Name")  // print environment vars
                            //bat """set payload="OVERWRITTEN BY JOB" """
							sh "python BuildEnvironment/Jenkins/CIPS/python/build.py -var icasevo" //${variantName}"
						}
					//}			
                }
            }
		}
	}
}