= 20141115-Checkstyle
[2014-11-15 22:08] >>> f:/goat-pc-data/mydropbox/Dropbox/trunksync/notes/20141008-Checkstyle.markdown

# 20141115-Checkstyle #

## links ##

- comefrom: [[20141008-Checkstyle]]

## 目標 ##

- LineLength 閾値を 80 から 100 に変更する。

## project ##

- echo "github, new repository, 20141115-Checkstyle"
- cd 'F:\goat-pc-data\ecworkspace'
- mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.github.tempest200903 -DartifactId=20141115-Checkstyle -DinteractiveMode=false
- mv 20141115-Checkstyle{,.import}
- git clone git@github.com:tempest200903/20141115-Checkstyle.git
- mv 20141115-Checkstyle.import/* 20141115-Checkstyle/
- rm -rf 20141115-Checkstyle.import
- cd 20141115-Checkstyle
- mvn eclipse:eclipse
- git add .
- git commit -m "mvn archetype:generate, mvn eclipse:eclipse" .
- git push

## src ##

- LineLength 90 である行を作る。

## pom.xml ##

- http://maven.apache.org/plugins/maven-checkstyle-plugin/usage.html
- http://mvnrepository.com/artifact/checkstyle/checkstyle
- pom.xml に <artifactId>checkstyle</artifactId> を追加する。

    ```
    > <reporting>
    >   <plugins>
    >    <plugin>
    >     <groupId>com.puppycrawl.tools</groupId>
    >     <artifactId>checkstyle</artifactId>
    >     <version>6.1</version>
    >     <reportSets>
    >      <reportSet>
    >       <reports>
    >        <report>checkstyle</report>
    >       </reports>
    >      </reportSet>
    >     </reportSets>
    >    </plugin>
    >   </plugins>
    >  </reporting>
    ```

- run 20141115-Checkstyle-site

    ```
    > [ERROR] Failed to execute goal org.apache.maven.plugins:maven-site-plugin:3.3:site (default-site) on project 20141115-Checkstyle: failed to get report for com.p
    > uppycrawl.tools:checkstyle: Failed to parse plugin descriptor for com.puppycrawl.tools:checkstyle:6.1 (C:\Users\simei\.m2\repository\com\puppycrawl\tools\checks
    > tyle\6.1\checkstyle-6.1.jar): No plugin descriptor found at META-INF/maven/plugin.xml -> [Help 1]
    > org.apache.maven.lifecycle.LifecycleExecutionException: Failed to execute goal org.apache.maven.plugins:maven-site-plugin:3.3:site (default-site) on project 201
    > 41115-Checkstyle: failed to get report for com.puppycrawl.tools:checkstyle
    ```

- pom.xml に <artifactId>maven-checkstyle-plugin</artifactId> を追加する。

    ```
    > <reporting>
    >   <plugins>
    >    <plugin>
    >     <!-- <groupId>com.puppycrawl.tools</groupId> <artifactId>checkstyle</artifactId> 
    >      <version>6.1</version> -->
    >     <groupId>org.apache.maven.plugins</groupId>
    >     <artifactId>maven-checkstyle-plugin</artifactId>
    >     <version>2.13</version>
    >     <reportSets>
    >      <reportSet>
    >       <reports>
    >        <report>checkstyle</report>
    >       </reports>
    >      </reportSet>
    >     </reportSets>
    >    </plugin>
    >   </plugins>
    >  </reporting>
    ```

- BUILD SUCCESS

## report ##

- file:///F:/goat-pc-data/ecworkspace/20141115-Checkstyle/target/site/checkstyle.html#com.github.tempest200903.App.java
    - report の LineLength をクリックすると checkstyle.sourceforge.net にジャンプする。
- http://checkstyle.sourceforge.net/config_sizes.html#LineLength

    ```
    > Properties
    > 
    > name	description	type	default value
    > ignorePattern	pattern for lines to ignore	regular expression	^$
    > max	maximum allowable line length	integer	80
    > Examples
    > 
    > To configure the check to accept lines up to 120 characters long:
    > 
    > <module name="LineLength">
    >     <property name="max" value="120"/>
    > </module>
    >         
    > To configure the check to ignore lines that begin with " * ", followed by just one word, such as within a Javadoc comment:
    > 
    > <module name="LineLength">
    >    <property name="ignorePattern" value="^ *\* *[^ ]+$"/>
    > </module>
    ```
- git commit -m "add checkstyle plugin to reporting" .

## pom.xml に configLocation を記す ##

- http://maven.apache.org/plugins/maven-checkstyle-plugin/examples/custom-checker-config.html
- http://maven.apache.org/plugins/maven-checkstyle-plugin/checkstyle-mojo.html
- pom.xml

    ```
    > <configuration>
    >      <configLocation>config/maven_checks.xml</configLocation>
    >     </configuration>
    ```

- mvn site
- file:///F:/goat-pc-data/ecworkspace/20141115-Checkstyle/target/site/checkstyle.html#com.github.tempest200903.App.java
    - LineLength 警告が出ない！

## config/maven_checks.xml の中身を見たい＆再利用したい ##

- C:\Users\simei\.m2\repository\org\apache\maven\plugins\maven-checkstyle-plugin\2.13\maven-checkstyle-plugin-2.13.jar
- この jar ファイルを展開する。 config/maven_checks.xml に実物がある。
- F:\goat-pc-data\ecworkspace\20141115-Checkstyle\config\maven_checks.xml

    ```
    > <module name="LineLength">
    >           <property name="max" value="120" />
    >           <property name="ignorePattern" value="@version|@see|@todo|TODO"/>
    >         </module>
    ```

- git commit -m "cp maven-checkstyle-plugin-2.13.jar/config/* config/"

## config を自作する ##

- import 文は LineLength 制限しない。
- cd F:\goat-pc-data\ecworkspace\20141115-Checkstyle\config
- cp maven_checks.xml my_checks.xml
- pom.xml

    ```
    >         <module name="LineLength">
    >           <property name="max" value="80" />
    >           <property name="ignorePattern" value="import"/>
    >         </module>
    ```

- mvn site
- file:///F:/goat-pc-data/ecworkspace/20141115-Checkstyle/target/site/checkstyle.html#com.github.tempest200903.App.java
    - ignorePattern にマッチする import 行は警告がでない。
    - マッチしない行は警告が出る。
- 目的達成！
- git commit -m "config を自作する。 import 文は LineLength 制限しない。"


## wiki ##

- cp f:/goat-pc-data/mydropbox/Dropbox/trunksync/notes/20141115-Checkstyle.markdown F:/goat-pc-data/ecworkspace/20141115-Checkstyle/wiki/20141115-Checkstyle.md
- git add .
- git commit -m "wiki" .
- git push
- https://github.com/tempest200903/20141115-Checkstyle/blob/master/wiki/20141115-Checkstyle.md

## timelog ##

- [2014-11-15 土 22:04] begin
- [2014-11-15 土 23:51] end

