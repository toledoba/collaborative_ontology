/*******************************************************\
    Universidad Politecnica de Madrid, Spain
    Copyright 2020 Ontology Engineering Group
    @autor: Jhon Toledo Barreto
\*******************************************************/


// -- Directory where the Project Files are located.  
def JOB_FILES_DIRECTORY
// -- Directory where the Platform Tools is located
def PLATFORM_TOOL_DIRECTORY
// -- Path of the Suite to execute
def SUITE_PATH
// -- Path of Ontology file
def ONTOLOGY
// --  Ontology Directory
def Ontology

pipeline {

  agent {
      label '!windows'
  }
  // -- Display a timestamp on the log.
  options{timestamps()}
  // -- Evironment
  environment {
      VERSION      = '1.0'
      WIDOCO       = '1.4.14'
      AR2TOOL      = 'v.1.0'
      VOCABLITE    = '1.0.2'
      Ontology_dir  = 'Ontology'  // -- name of the directory where the ontology is located
      Ontology_path     = 'Ontology/alo.owl' // -- path where the ontology is located
  }

  stages {

    // Parameters needed: JOB_FILES_DIRECTORY, PLATFORM_TOOL_DIRECTORY, EMULATOR_DIRECTORY, SUITE_PATH
    // JOB_NAME, JOB_APPIUM_SUITE
    // ------------------------------------
    // -- STAGE: Initial Configuration
    // ------------------------------------
    stage("Initial Configuration") {
      steps {
        script {
          if (isUnix()) {
            echo "Unix"
            echo "${env.WORKSPACE}"
          } else {
            print "Windows"
            // -- Path Workspace
            echo "${env.WORKSPACE}"

          } 
            // -- Set the Directory of the files in the workspace
            JOB_FILES_DIRECTORY = "${env}"
            echo "directorio $JOB_FILES_DIRECTORY"
            // -- Set the suite name and route parameter
            //SUITE_PATH = "src/test/resources/suites/"+"${JOB_APPIUM_SUITE}"+".xml" 
        }
        // -- Clean Workspace
        echo "Clean Workspace"
        cleanWs()
        sh "wget https://raw.githubusercontent.com/albaizq/OpenADRontology/master/ontology/openADRontology.owl"
        sh "wget https://raw.githubusercontent.com/opencitydata/vocab-sector-publico-empleo/master/Ontology/empleo.owl"
        //sh "wget https://raw.githubusercontent.com/ahmad88me/GSSO/master/gsso.owl"
        //sh "mv *.owl ${Ontology_dir} "
        def myontologies = ontologies()
        for (int i = 0; i < myontologies.size(); ++i) {
            echo "Testing the ${myontologies[i]} "
            sh "mv ${myontologies[i]} ${Ontology_dir}"
        }
      }
    }
    // Parameters needed:
    // ------------------------------------
    // -- STAGE: Widoco
    // ------------------------------------
    stage('Widoco'){
      steps{
        script{  
          def exists = fileExists "widoco-${WIDOCO}-jar-with-dependencies.jar"
          if (!exists) {
            sh "wget https://github.com/dgarijo/Widoco/releases/download/v${WIDOCO}/widoco-${WIDOCO}-jar-with-dependencies.jar"
          } else {
            echo "Building Widoco version ${WIDOCO}"
          }
        }
        script{
            def myontologies = ontologies()
            for (int i = 0; i < myontologies.size(); ++i) {
                echo "Testing the ${myontologies[i]} "
                sh "java -jar widoco-${WIDOCO}-jar-with-dependencies.jar -ontFile Ontology/${myontologies[i]} -outFolder Documents-${myontologies[i]}  -oops -rewriteAll -lang en-es -webVowl -uniteSections"        
            }
        }
        
         //sh "java -jar widoco-${WIDOCO}-jar-with-dependencies.jar -ontFile ${Ontology_path} -outFolder Documents  -oops -rewriteAll -lang en-es -webVowl -uniteSections"        
         
      }
    }
    // Parameters needed:
    // ------------------------------------
    // -- STAGE: Oops!
    // ------------------------------------
    stage('Oops!') {
      steps {
          sh "pwd"
          //for this test it's integrated with widoco
          //sh './oops.sh'
      }
    }
    // Parameters needed:
    // ------------------------------------
    // -- STAGE: AR2Tool
    // ------------------------------------
    stage('AR2Tool') {
      steps {
        script{
          def exists = fileExists "ar2dtool-0.1.jar"
          if(!exists){
            sh "wget https://github.com/idafensp/ar2dtool/archive/${AR2TOOL}.tar.gz"
            sh "tar -xf ${AR2TOOL}.tar.gz"
            sh "rm ${AR2TOOL}.tar.gz"
            sh "mv ar2dtool-${AR2TOOL}/lib/ar2dtool-0.1.jar . "
            sh "rm -rf ar2dtool-${AR2TOOL}"
          }else{
             echo "Building AR2Tool version ${AR2TOOL}"
          }
          
          //java -jar ar2dtool.jar -i PathToInputRdfFile -o FileToOutputFile -t OutputFileType -c PathToConfFile -GENERATE_FLAGS [-d]
          //sh "java -jar ar2dtool-0.1.jar -i ${Ontology_path} -o ar2dtoolOutputFile"
        }
      }
    }
    // Parameters needed:
    // ------------------------------------
    // -- STAGE: VocabLite
    // ------------------------------------
    stage('VocabLite') {
      steps {
        script{
          def exists = fileExists "vocabLite-${VOCABLITE}-jar-with-dependencies.jar"
          if(!exists){
            sh "wget https://github.com/dgarijo/vocabLite/releases/download/${VOCABLITE}/vocabLite-${VOCABLITE}-jar-with-dependencies.jar"
          }else{
             echo "Building VocabLite version ${VOCABLITE}"
          }
            sh "java -jar vocabLite-${VOCABLITE}-jar-with-dependencies.jar -i ${Ontology_dir} -o vocabLite"
        }
      }
    }
  }
}

def ontologies() {
    return ["openADRontology.owl", "empleo.owl", "alo.owl"]
}

