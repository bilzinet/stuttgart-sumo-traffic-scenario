## Additional instructions to compress simulation output

SUMO generates a lot of data, especially when using Floating Car Data (FCD) output. FCD output records the position, speed, and other attributes of each vehicle at every time step, which can lead to very large files. To manage the size of these files, you can use a named pipe to compress the output on-the-fly using **gzip**, which significantly reduces the file size without losing any data.


### Named Pipe

A named pipe (or FIFO for First In First Out) is a method for inter-process communication. It acts like a file, but data written to it can be read by another process simultaneously. This is useful for streaming data from one process to another, such as from SUMO to **gzip**.


### Setting Up Named Pipe with gzip for SUMO

1. Create a Named Pipe:

```shell
mkfifo /tmp/fcd_fifo

```

2. Start **gzip** to Compress the Named Pipe:

```shell
gzip < /tmp/fcd_fifo > /path/to/output/fcd_output.xml.gz &

```

This command tells **gzip** to read from the named pipe **/path/to/fifo**, compress the data, and write it to **/path/to/output/fcd_output.xml.gz**

3. Configure SUMO to Write FCD Output to the Named Pipe:

In your SUMO configuration file, set the FCD output to the named pipe:

```shell
<configuration>
    <input>
        <net-file value="intersection.net.xml"/>
        <route-files value="intersection.rou.xml"/>
        <additional-files value="intersection.add.xml"/>
    </input>
    <time>
        <begin value="0"/>
        <end value="3600"/>
    </time>
    <output>
        <fcd-output value="/tmp/fcd_fifo"/>
    </output>
</configuration>

```

This tells SUMO to write the FCD output to the named pipe **/path/to/fifo**.


### Benefits
 - Reduced File Size: Compressing the output on-the-fly can drastically reduce the file size.
 - Efficiency: Using a named pipe avoids the need to create large intermediate files before compression.
 - Seamless Integration: The data flows directly from SUMO to **gzip**, streamlining the process.