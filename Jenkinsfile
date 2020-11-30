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
        
        //cleanWs()
        //sh "wget -nc https://raw.githubusercontent.com/albaizq/OpenADRontology/master/ontology/openADRontology.owl -P ${Ontology_dir}"
        //sh "wget -nc https://raw.githubusercontent.com/opencitydata/vocab-sector-publico-empleo/master/Ontology/empleo.owl -P ${Ontology_dir}"
        //sh "wget https://raw.githubusercontent.com/ahmad88me/GSSO/master/gsso.owl"
        //sh "mv *.owl ${Ontology_dir}/ "
        /*script{
          def myontologies = ontologies()
          for (int i = 0; i < myontologies.size(); ++i) {
              echo "Testing the ${myontologies[i]} "
              sh "mv ${myontologies[i]} ${Ontology_dir}"
          }
        }*/
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

           // sh "mv --force *.owl ${Ontology_dir} "
            sh "echo 'run,elapsed_time,kernel_mode,User_mode,memory_max,memory_average,file'>>time.csv"
            def myontologies = ontologies()
            for (int i = 0; i < myontologies.size(); ++i) {
                echo "Testing the ${myontologies[i]} "
                sh " time  -f 'elapsed_time,kernel_mode,user_mode,memory_max,memory_average,file\n%e,%S,%U,%M,%K,${myontologies[i]}' -o tmp_time.csv java -jar widoco-${WIDOCO}-jar-with-dependencies.jar -ontFile Ontology/${myontologies[i]} -outFolder Documents-${myontologies[i]}  -oops -rewriteAll -lang en-es -webVowl -uniteSections"        
                sh "tail -1 tmp_time.csv >> time.csv"
                sh "rm tmp_time.csv"
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
            
            sh "time  -f 'elapsed_time,kernel_mode,user_mode,memory_max,memory_average\n%e,%S,%U,%M,%K' -o tmp_time.csv java -jar vocabLite-${VOCABLITE}-jar-with-dependencies.jar -i ${Ontology_dir} -o vocabLite"
            sh "rm *.jar"
        }
      }
    }
  }
}

def ontologies() {
  return ["alo.owl",
"netex_accounting_version.xsd.rdf",
"netex_address_support.xsd.rdf",
"netex_availabilityCondition_support.xsd.rdf",
"netex_availabilityCondition_version.xsd.rdf",
"netex_country_support.xsd.rdf",
"netex_dayType_propertiesOfDay.xsd.rdf",
"netex_dayType_support.xsd.rdf",
"netex_dayType_version.xsd.rdf",
"netex_equipment_support.xsd.rdf",
"netex_equipment_version.xsd.rdf",
"netex_facility_support.xsd.rdf",
"netex_facility_version.xsd.rdf",
"netex_facilityUic_support.xsd.rdf",
"netex_mode_support.xsd.rdf",
"netex_mode_version.xsd.rdf",
"netex_notice_support.xsd.rdf",
"netex_notice_version.xsd.rdf",
"netex_otherOrganisation_support.xsd.rdf"]
}

