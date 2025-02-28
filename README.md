plugins {
    id 'java'
    id 'org.apache.cxf.builder' version '4.0.5' // Ensure correct CXF plugin version
}

dependencies {
    // Apache CXF dependencies (Jakarta compatible)
    implementation 'org.apache.cxf:cxf-rt-frontend-jaxws:4.0.5'
    implementation 'org.apache.cxf:cxf-rt-transports-http:4.0.5'
    
    // Jakarta XML and JAXB dependencies
    implementation 'jakarta.xml.bind:jakarta.xml.bind-api:4.0.0'
    implementation 'org.glassfish.jaxb:jaxb-runtime:4.0.3'
    implementation 'jakarta.xml.ws:jakarta.xml.ws-api:4.0.0'
    implementation 'jakarta.annotation:jakarta.annotation-api:2.1.1'
}

cxfCodegen {
    wsdl2java { NamedDomainObjectContainer<WsdlOption> it ->
        AcquisitionsService {
            wsdl = file("${projectDir}/src/main/wsdl/AcquisitionsServiceBean.wsdl")
        }
        ECMContentServiceAOBeanService {
            wsdl = file("${projectDir}/src/main/wsdl/ECMContentServiceAOBeanService.wsdl")
        }
        CorrespondanceRequestService {
            wsdl = file("${projectDir}/src/main/wsdl/CorrespondanceRequestService.wsdl")
        }
    }

    wsdl2java.configureEach { it ->
        outputDir = file("${buildDir}/generated-sources")
        markGenerated = true
        
        // Force Jakarta namespace
        args.addAll(
            '-xjc', '-extension',
            '-verbose',
            '-encoding', 'UTF-8',
            '-autoNameResolution',
            '-exsh', 'true',
            '-fe', 'jaxws21',  // Ensures JAX-WS 2.1 compatibility (Jakarta)
            '-p', 'com.yourcompany.generated',
            '-client',  // If you need client classes
            '-server',  // If you need server classes
            '-mark-generated',
            '-validate',
            '-jaxb21'  // **Forces Jakarta JAXB instead of javax**
        )
    }
}

// Ensure wsdl2java runs before compileJava
compileJava.dependsOn wsdl2java
