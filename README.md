![StaroJSB](https://github.com/starohub/starojsb/raw/master/resources/images/starojsb-64.png)

# Staro JavaScript Sandbox
### Documents

[API for JavaScript](http://jsb.starohub.com/api.html)

[API for module building](http://jsb.starohub.com/xapi.html)

### Release

[Release 0.1.9](https://github.com/starohub/starojsb/releases/tag/0.1.9)

### Maven

```
        <!-- https://github.com/starohub/staroplaties/releases/tag/0.0.2 -->
        <dependency>
            <groupId>com.starohub.platies</groupId>
            <artifactId>staroplaties</artifactId>
            <version>0.0.2</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/lib/staroplaties.jar</systemPath>
        </dependency>

        <!-- https://github.com/starohub/starodiscus/releases/tag/0.0.3 -->
        <dependency>
            <groupId>com.starohub.discus</groupId>
            <artifactId>starodiscus</artifactId>
            <version>0.0.3</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/lib/starodiscus.jar</systemPath>
        </dependency>

        <!-- https://github.com/starohub/starojsb/releases/tag/0.1.9 -->
        <dependency>
            <groupId>com.starohub.jsb</groupId>
            <artifactId>starojsb</artifactId>
            <version>0.1.9</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/lib/starojsb.jar</systemPath>
        </dependency>
```

### Usage

```
public class TestRunner {
    public static void main(String[] args) {
        try {
            String jsFile = args[0];
            String dataFolder = args[1];

            int timeout = 60;
            String js = readText(jsFile);

            SBObject sbo = new SBObject(js, timeout, dataFolder);
            int a = 20;
            sbo.set("a", a);
            int a2 = (Integer)sbo.store().get("a", 0);
            int a3 = sbo.getInteger("a", 0);
            sbo.sandbox().machine().log().info("a = " + a + ", a2 = " + a2 + ", a3 = " + a3);

            int b = (Integer)sbo.exec("f1", a);
            int b2 = (Integer)sbo.store().get("a", 0);
            int b3 = sbo.getInteger("a", 0);
            sbo.sandbox().machine().log().info("b = " + b + ", b2 = " + b2 + ", b3 = " + b3);

            int c = (Integer)sbo.exec("f2", a);
            int c2 = (Integer)sbo.store().get("a", 0);
            int c3 = sbo.getInteger("a", 0);
            sbo.sandbox().machine().log().info("c = " + c + ", c2 = " + c2 + ", c3 = " + c3);

            sbo.exec("f3", null);
            //sbo.exec("f4", null);
        } catch (Throwable e) {
            if (e instanceof SException) {
                System.out.println(((SException)e).stacktrace());
            } else {
                e.printStackTrace();
            }
        }
    }

    public static void writeText(String filename, String content) throws Exception {
        FileOutputStream fos = new FileOutputStream(filename);
        fos.write(content.getBytes("UTF-8"));
        fos.close();
    }

    public static String readText(String filename) throws Exception {
        FileInputStream fis = new FileInputStream(filename);
        byte[] buffer = new byte[fis.available()];
        fis.read(buffer);
        fis.close();
        return new String(buffer, "UTF-8");
    }
}
```

### JavaScript

```
function __exec__(data) {
	log().info('func: ' + data.func());
	if (data.func() == 'main') {
		data.func('f3');
		__exec__(data);
		data.func('f4');
		__exec__(data);
		lang().sleep(1000 * 5);
		var output = util().newHashMap();
		output.put('result', 'completed');
		data.output(output);
	}	
	if (data.func() == 'f1') {
		var a = getInteger('a', 0);
		a += 10;
		a = lang().newInteger(a);
		set('a', a);
		data.output(a);
	}
	if (data.func() == 'f2') {
		var a = getInteger('a', 0);
		a += 50;
		a = lang().newInteger(a);
		set('a', a);
		data.output(a);
	}
	if (data.func() == 'f3') {
		var d = pkg('jsb.lang').newData();
		d.input('John');
		lang().thread('t1', d);
		
		d = lang().newData();
		d.input('Mai');
		lang().thread('t2', d);	
	}
	if (data.func() == 'f4') {
		var f = mnt().newFile('/T01.js');
		var js = lang().newString(f.readFile(), 'UTF-8');
		log().info(js);
	}	
}

function t1(data) {
	pkg('jsb.log').info('Hello ' + data.input());
	lang().sleep(1000 * 2);
	log().info('Bye ' + data.input());	
}

function t2(data) {
	log().info('Chao ' + data.input());
	lang().sleep(1000 * 1);
	pkg('jsb.log').info('Tam biet ' + data.input());	
}

```

### Test results

```
Aug 24, 2020 9:41:38 AM jsb.log.Package info
INFO: a = 20, a2 = 20, a3 = 20
Aug 24, 2020 9:41:38 AM jsb.log.Package info
INFO: func: f1
Aug 24, 2020 9:41:38 AM jsb.log.Package info
INFO: b = 30, b2 = 30, b3 = 30
Aug 24, 2020 9:41:38 AM jsb.log.Package info
INFO: func: f2
Aug 24, 2020 9:41:38 AM jsb.log.Package info
INFO: c = 80, c2 = 80, c3 = 80
Aug 24, 2020 9:41:38 AM jsb.log.Package info
INFO: func: f3
Aug 24, 2020 9:41:38 AM jsb.log.Package info
INFO: Hello John
Aug 24, 2020 9:41:38 AM jsb.log.Package info
INFO: Chao Mai
Aug 24, 2020 9:41:39 AM jsb.log.Package info
INFO: Tam biet Mai
Aug 24, 2020 9:41:40 AM jsb.log.Package info
INFO: Bye John
```
