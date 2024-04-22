### Steps to install Spark on Mac

To install Apache Spark on macOS, you can follow these steps:

1. First, you need to have Java installed on your system. You can check if Java is installed by running the following command in your terminal:

```bash
java -version
```

If Java is not installed, you can install it using Homebrew:

```bash
brew install openjdk@11
```

And then add it to your shell profile:

```bash
echo 'export PATH="/usr/local/opt/openjdk@11/bin:$PATH"' >> ~/.zshrc
```

2. Next, install Scala, as Spark is written in Scala:

```bash
brew install scala
```

3. Now, you can install Apache Spark:

```bash
brew install apache-spark
```

4. After the installation is complete, you need to add `SPARK_HOME` to your shell profile:

```bash
echo 'export SPARK_HOME="/usr/local/Cellar/apache-spark/3.1.2/libexec"' >> ~/.zshrc
echo 'export PATH="$SPARK_HOME/bin:$PATH"' >> ~/.zshrc
```

Please replace `3.1.2` with your installed Spark version.

5. Finally, source your shell profile to apply the changes:

```bash
source ~/.zshrc
```

6. You can verify the installation by running:

```bash
spark-shell
```

This should start the Spark shell. If everything is set up correctly, you should see a welcome message and a scala prompt.

Please note that the exact steps might vary slightly depending on the version of macOS and Spark you are using.
