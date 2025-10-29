# ZooCreeper

## What is ZooCreeper?

ZooCreeper is a command-line utility for backing up and restoring data from [Apache ZooKeeper](https://zookeeper.apache.org/). ZooKeeper is a centralized service for maintaining configuration information, naming, providing distributed synchronization, and providing group services. Given its critical role in distributed systems, having a reliable backup of your ZooKeeper data is essential.

ZooCreeper simplifies this process by providing a simple and flexible way to create and restore backups.

## Features

*   Ignores ephemeral nodes by default.
*   Customize which ZooKeeper paths are backed up or restored using `--exclude` / `--include` regular expressions.
*   Optional compression of the backup file using GZIP.

## Build

This project requires Maven 3.0 and Java 8+ to build. To build, run this command in the top-level directory:

```bash
mvn clean package
```

This will create an executable, shaded jar file named `target/zoocreeper-1.0-SNAPSHOT.jar`.

## Usage

### Backup

To see the available options for creating a backup, run:

```bash
java -jar target/zoocreeper-1.0-SNAPSHOT.jar --help
```

The only required option is `-z`/`--zk-connect` which is a standard ZooKeeper connection string.

**Example: Basic Backup**

This command will back up all non-ephemeral nodes from the ZooKeeper instance running on `localhost:2181` and print the JSON output to the console.

```bash
java -jar target/zoocreeper-1.0-SNAPSHOT.jar --zk-connect localhost:2181
```

**Example: Backup to a File**

This command will back up the ZooKeeper data to a file named `zookeeper-backup.json`.

```bash
java -jar target/zoocreeper-1.0-SNAPSHOT.jar --zk-connect localhost:2181 > zookeeper-backup.json
```

**Example: Compressed Backup**

This command will create a GZIP compressed backup file.

```bash
java -jar target/zoocreeper-1.0-SNAPSHOT.jar --zk-connect localhost:2181 --compress -f zookeeper-backup.json.gz
```

**Example: Excluding Paths**

This command will exclude all paths under `/foo` and `/bar` from the backup.

```bash
java -jar target/zoocreeper-1.0-SNAPSHOT.jar --zk-connect localhost:2181 --exclude "/foo/.*" --exclude "/bar/.*"
```

### Restore

To see the available options for restoring a backup, run:

```bash
java -cp target/zoocreeper-1.0-SNAPSHOT.jar com.boundary.zoocreeper.Restore --help
```

**Example: Restore from a File**

This command will restore the ZooKeeper data from the `zookeeper-backup.json` file.

```bash
java -cp target/zoocreeper-1.0-SNAPSHOT.jar com.boundary.zoocreeper.Restore --zk-connect localhost:2181 < zookeeper-backup.json
```

**Example: Restore from a Compressed File**

This command will restore from a GZIP compressed backup file.

```bash
gunzip -c zookeeper-backup.json.gz | java -cp target/zoocreeper-1.0-SNAPSHOT.jar com.boundary.zoocreeper.Restore --zk-connect localhost:2181
```

**Example: Excluding Paths During Restore**

This command will restore the ZooKeeper data but exclude all paths under `/foo` and `/bar` from being restored. This is useful for avoiding permission issues or restoring only necessary parts of the backup.

```bash
gunzip -c zookeeper-backup.json.gz | java -cp target/zoocreeper-1.0-SNAPSHOT.jar com.boundary.zoocreeper.Restore --zk-connect localhost:2181 --exclude "/foo/.*" --exclude "/bar/.*"
```

**Example: Restoring with Overwrite, No ACLs, and Specific Exclusions**

This command demonstrates a more robust restore operation, overwriting existing nodes, ignoring ACLs, and specifically excluding the `/zookeeper/config` path due to potential permission issues.

```bash
java -cp target/zoocreeper-1.0-SNAPSHOT.jar com.boundary.zoocreeper.Restore --zk-connect localhost:2181 --overwrite-existing --no-acls --exclude "/zookeeper/config(|/.*)" < zookeeper-backup.json
```

### Helper Script

Also included is a bash helper script:

```bash
# Dump the ZooKeeper data to a file
./zoocreeper dump -z 127.0.0.1 > dumpfile.json

# Load the data from the file back into ZooKeeper
cat dumpfile.json | ./zoocreeper load -z 127.0.0.1
```

## License

This project is licensed under the Apache License 2.0. See the [LICENSE](./LICENSE) file for details.
