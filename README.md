# KasperskyOS modification of PHP Interpreter

>This version of PHP Interpreter (Zend) is adapted for KasperskyOS. The PHP Interpreter operates in FastCGI mode.

## What is a PHP Interpreter for KasperskyOS?

The PHP Interpreter for KasperskyOS is based on the [PHP-8.2](https://github.com/php/php-src/tree/PHP-8.2). Please refer to [README.md](https://github.com/php/php-src/blob/master/README.md) for more information about the original PHP Interpreter not related to this project.

The PHP Interpreter for KasperskyOS has the following limitations:

* Currently, subprocess creation isn't available in KasperskyOS Community Edition. All client requests to the PHP Interpreter are processed sequentially: a new request is executed only when a previous request has finished processing.

For additional details on KasperskyOS, including its limitations and known issues, please refer to the [KasperskyOS Community Edition Online Help](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=community_edition).

## Table of contents

- [PHP Interpreter for KasperskyOS](#php-interpreter-for-kasperskyos)
  - [What is a PHP Interpreter for KasperskyOS?](#what-is-a-php-interpreter-for-kasperskyos)
  - [Table of contents](#table-of-contents)
  - [Getting started](#getting-started)
    - [Prerequisites](#prerequisites)
    - [Building the PHP Interpreter](#building-the-php-interpreter)
    - [Installing and removing the PHP Interpreter](#installing-and-removing-the-php-interpreter)
  - [Usage](#usage)
    - [Examples](#examples)
  - [Testing the PHP Interpreter](#testing-the-php-interpreter)
  - [Acknowledgment](#acknowledgment)
  - [Contributing](#contributing)
  - [License](#license)

## Getting started

### Prerequisites

1. [Install](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=sdk_install_and_remove)
KasperskyOS Community Edition SDK. You can download the latest version of the KasperskyOS Community Edition for free from
[os.kaspersky.com](https://os.kaspersky.com/development/). The minimum required version of KasperskyOS Community Edition SDK is 1.3.
For more information, see [System requirements](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=system_requirements).
1. Copy project sources files to your home directory. All files that are required to build the PHP Interpreter for KasperskyOS and examples of KasperskyOS-based solutions are located in the following directory:
   ```
   ./kos
   ```
1. Set the environment variable `SDK_PREFIX` to `/opt/KasperskyOS-Community-Edition-<version>`, where `version` is the version of the KasperskyOS Community Edition SDK that you installed. To do this, run the following command:
   ```
   $ export SDK_PREFIX=/opt/KasperskyOS-Community-Edition-<version>
   ```

### Building the PHP Interpreter

Run the following script to build the PHP Interpreter for KasperskyOS:
```
./kos/php/cross-build.sh
```
The PHP Interpreter for KasperskyOS is built using the CMake build system, which is provided in the KasperskyOS Community Edition SDK.

### Installing and removing the PHP Interpreter

To install the PHP Interpreter for KasperskyOS to the KasperskyOS Community Edition SDK, run the following script with root privileges:
```
./kos/php/install.sh
```

To remove the PHP Interpreter for KasperskyOS from the KasperskyOS Community Edition SDK, run the following script with root privileges:
```
./kos/php/uninstall.sh
```

Note:
In case of using the sudo command for installing, there should be used the following command:
```
sudo ./kos/php/install.sh /opt/KasperskyOS-Community-Edition-<version>
```
To uninstall there should be used the following command:
```
sudo ./kos/php/uninstall.sh /opt/KasperskyOS-Community-Edition-<version>
```

[⬆ Back to Top](#Table-of-contents)

## Usage

When you develop a KasperskyOS-based solution, use the [recommended structure of project directories](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=cmake_using_sdk_cmake) to simplify usage of CMake scripts.

To include the PHP Interpreter in your KasperskyOS-based solution, follow these steps:

1. Add the `find_package()` command to the `./CMakeLists.txt` root file to find and load the `php` package.
   ```
   find_package (php REQUIRED)
   ```
   For more information about the `./CMakeLists.txt` root file, see the [KasperskyOS Community Edition Online Help](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=cmake_lists_root).
1. Add the `Fpm` program to a list of program executable files defined in the `./einit/CMakeLists.txt` file as follows:
   ```
   set (ENTITIES
        Fpm
        ...)
   ```
   For more information about the `./einit/CMakeLists.txt` file for building the `Einit` initializing program, see [KasperskyOS Community Edition Online Help](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=cmake_lists_einit).
1. Specify a list of IPC channels that connect the `Fpm` program to `VfsNet` and `VfsRamFs` programs in the `./einit/src/init.yaml.in` template file. For more information about the `init.yaml.in` template file, see [KasperskyOS Community Edition Online Help](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=cmake_yaml_templates).
1. Create a solution security policy description in the `./einit/src/security.psl.in` template file. For more information about the `security.psl.in` template file, see [KasperskyOS Community Edition Online Help](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=cmake_psl_templates).
1. Add PHP and nginx configuration files to the directory `./resources`.

### Examples

* [`./kos/examples/fpm`](kos/examples/fpm)—Version of the FastCGI Process Manager (FPM) adapted for use on KasperskyOS.
* [`./kos/examples/web_server`](kos/examples/web_server)—KasperskyOS-based dynamic web server.

[⬆ Back to Top](#Table-of-contents)

## Testing the PHP Interpreter

The test suite for the original PHP Interpreter is used to test the PHP Interpreter for KasperskyOS, but testing systems are different. The testing system for KasperskyOS consists of two programs: [`php.Server`](kos/tests/server) and [`php.Client`](kos/tests/client). The `php.Client` program parses PHPT files and sends a request, which contains a PHP script, to the `php.Server` program. The `php.Server` program executes the request and returns а result to the `php.Client` using аn HTTP response. The `php.Client` compares the received response with the expected result.

To run the tests, execute the following script:
```
./kos/tests/cross-build.sh qemu
```

To build an example to run on a Raspberry Pi 4 B or Radxa ROCK 3A, use the following command:
```
$ ./kos/tests/cross-build.sh hw
```
For more information about running tests on Raspberry Pi 4 B or Radxa ROCK 3A see the following [link](https://click.kaspersky.com/?hl=en-us&link=online_help&pid=kos&version=1.3&customization=KCE&helpid=running_sample_programs_rpi).

Note:
In case of building tests to run on Radxa ROCK 3A, `kos-image` may not run correctly (according to the known size limitations of `kos-image` in the below [link](https://support.kaspersky.com/help/KCE/1.3/en-US/limitations_and_known_problems.htm)). To avoid this, you should shift the FDT loadable address in u-boot, for example:
```
setenv fdt_addr_r 0xa0000000
```
If you do not want to shift the FDT loadable address on every boot, you can also save the u-boot environment with the following command:
```
saveenv
```

If you want to save test results to a file, go to the directory `./kos/tests` and run the following command:
```
./kos/tests/cross-build.sh qemu > log.txt
```

[⬆ Back to Top](#Table-of-contents)

## Acknowledgment

This product includes PHP software, freely available from <http://www.php.net/software/>.

## Contributing

Only KasperskyOS-specific changes can be approved. See [CONTRIBUTING.md](CONTRIBUTING.md) for detailed instructions on code contribution.

## License

This project is licensed under the terms of the PHP License. See [LICENSE](LICENSE) for more information.

[⬆ Back to Top](#Table-of-contents)
