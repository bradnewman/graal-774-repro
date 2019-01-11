# Reproducer for GraalVM #774 #

Reproduces [oracle/graal#774](https://github.com/oracle/graal/issues/774) with GraalVM RC9 and RC10; produces a working binary with GraalVM RC8.

Based on the Kraal example project.

## Building ##

With GraalVM installed locally:

    ./gradlew build
    native-image \
        --static \
        --report-unsupported-elements-at-runtime \
        -jar build/fatjar/example.jar
    ./example

Or, using a Docker container with GraalVM and building the example webapp into a 26MB Docker image:

    ./gradlew build
    docker build -f Dockerfile -t kraal-example build/fatjar
    docker run --rm -P kraal-example

## Result ##

    StartServer [
    /graalvm/bin/java \
    -Xbootclasspath/a:/graalvm/jre/lib/boot/graaljs-scriptengine.jar:/graalvm/jre/lib/boot/graal-sdk.jar \
    -cp \
    /graalvm/jre/lib/svm/builder/pointsto.jar:/graalvm/jre/lib/svm/builder/objectfile.jar:/graalvm/jre/lib/svm/builder/svm.jar:/graalvm/jre/lib/jvmci/jvmci-api.jar:/graalvm/jre/lib/jvmci/jvmci-hotspot.jar:/graalvm/jre/lib/jvmci/graal-management.jar:/graalvm/jre/lib/jvmci/graal.jar \
    -XX:+UnlockExperimentalVMOptions \
    -XX:+EnableJVMCI \
    -XX:-UseJVMCICompiler \
    -Dtruffle.TrustAllTruffleRuntimeProviders=true \
    -d64 \
    -noverify \
    -XX:-UseJVMCIClassLoader \
    -Xss10m \
    -Duser.country=US \
    -Duser.language=en \
    -Dgraalvm.version=1.0.0-rc10 \
    -Dorg.graalvm.version=1.0.0-rc10 \
    -Dcom.oracle.graalvm.isaot=true \
    -Djvmci.class.path.append=/graalvm/jre/lib/jvmci/graal.jar \
    -Xmx837758156 \
    -Xms837758156 \
    com.oracle.svm.hosted.server.NativeImageBuildServer \
    -port=0 \
    -logFile=/root/.native-image/machine-id-61416584cb417f53bdd8abd092d38401/session-id-1/server-id-824655918bf0e0bed9d92cf27c39c626a6835f15a1d2b3b4ee417b795dbca50b4858f417f0741c14e1c4d314a9332fc486908c9f52513258519e3b75b469233b/server.log
    ]
    Build on Server(pid: 10, port: 34633)*
    SendBuildRequest [
    -task=com.oracle.svm.hosted.NativeImageGeneratorRunner
    -imagecp
    /graalvm/jre/lib/svm/builder/pointsto.jar:/graalvm/jre/lib/svm/builder/objectfile.jar:/graalvm/jre/lib/svm/builder/svm.jar:/graalvm/jre/lib/jvmci/jvmci-api.jar:/graalvm/jre/lib/jvmci/jvmci-hotspot.jar:/graalvm/jre/lib/jvmci/graal-management.jar:/graalvm/jre/lib/jvmci/graal.jar:/graalvm/jre/lib/boot/graaljs-scriptengine.jar:/graalvm/jre/lib/boot/graal-sdk.jar:/graalvm/jre/lib/svm/library-support.jar:/:/example.jar
    -H:Path=/
    -H:Kind=STATIC_EXECUTABLE
    -H:+ReportUnsupportedElementsAtRuntime
    -H:Class=com.hpe.kraal.example.AppKt
    -H:Name=example
    -H:CLibraryPath=/graalvm/jre/lib/svm/clibraries/linux-amd64
    ]
    [example:10]    classlist:   5,035.93 ms
    [example:10]        (cap):   1,456.61 ms
    [example:10]        setup:   4,398.29 ms
    warning: unknown locality of class Lkotlin/coroutines/CoroutineContext$plus$1;, assuming class is not local. To remove the warning report an issue to the library or language author. The issue is caused by Lkotlin/coroutines/CoroutineContext$plus$1; which is not following the naming convention.
    [example:10]   (typeflow):  40,936.81 ms
    [example:10]    (objects):  19,861.72 ms
    [example:10]   (features):   2,143.01 ms
    [example:10]     analysis:  63,678.91 ms
    [example:10]     universe:   2,016.85 ms
    [example:10]      (parse):  14,594.65 ms
    [example:10]     (inline):   9,380.31 ms
    [example:10]    (compile):  68,145.04 ms
    [example:10]      compile:  94,150.28 ms
    [example:10]        image:      16.81 ms
    fatal error: com.oracle.svm.core.util.VMError$HostedError: Objects with relocatable pointers must be immutable
            at com.oracle.svm.core.util.VMError.guarantee(VMError.java:85)
            at com.oracle.svm.hosted.image.NativeImageHeap.choosePartition(NativeImageHeap.java:492)
            at com.oracle.svm.hosted.image.NativeImageHeap.addObjectToBootImageHeap(NativeImageHeap.java:451)
            at com.oracle.svm.hosted.image.NativeImageHeap.addObject(NativeImageHeap.java:271)
            at com.oracle.svm.hosted.image.NativeImageCodeCache.addConstantToHeap(NativeImageCodeCache.java:369)
            at com.oracle.svm.hosted.image.NativeImageCodeCache.addConstantsToHeap(NativeImageCodeCache.java:356)
            at com.oracle.svm.hosted.NativeImageGenerator.doRun(NativeImageGenerator.java:882)
            at com.oracle.svm.hosted.NativeImageGenerator.lambda$run$0(NativeImageGenerator.java:401)
            at java.util.concurrent.ForkJoinTask$AdaptedRunnableAction.exec(ForkJoinTask.java:1386)
            at java.util.concurrent.ForkJoinTask.doExec(ForkJoinTask.java:289)
            at java.util.concurrent.ForkJoinPool$WorkQueue.runTask(ForkJoinPool.java:1056)
            at java.util.concurrent.ForkJoinPool.runWorker(ForkJoinPool.java:1692)
            at java.util.concurrent.ForkJoinWorkerThread.run(ForkJoinWorkerThread.java:157)
    Error: Processing image build request failed
