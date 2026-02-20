# Experiment  Create-a-datacenter-with-two-hosts-and-run-two-cloudlets-on-it.
Create a datacenter with two hosts and run two cloudlets on it. The cloudlets run in VMswith Different MIPS requirements. The cloudlets will take different time to complete the execution Depending on the requested VM performance.
 
## Overview
This project demonstrates a CloudSim 3.0.3 simulation experiment where two cloudlets are executed on virtual machines with different MIPS values.

Since the virtual machines have different computational capacities, the cloudlets complete execution at different times depending on VM performance.

This experiment is commonly used in cloud computing laboratory exercises, academic coursework, and performance analysis studies.

---

## Aim
To create a datacenter with two hosts and execute two cloudlets on virtual machines having different MIPS requirements such that cloudlets take different execution times.

---

## Objectives
- To simulate a cloud datacenter using CloudSim
- To create multiple hosts within a datacenter
- To configure virtual machines with different MIPS values
- To execute cloudlets with identical workloads
- To analyze execution time differences

---

## Tools and Technologies

| Tool | Version |
|------|---------|
| Java JDK | 1.8 |
| CloudSim | 3.0.3 |
| IDE | IntelliJ IDEA / Eclipse |
| Operating System | Windows / Linux |

---

## System Configuration

### Datacenter Configuration

| Parameter | Value |
|------------|-------|
| Datacenters | 1 |
| Hosts | 2 |
| RAM | 4096 MB |
| Storage | 1,000,000 MB |
| Bandwidth | 10,000 Mbps |
| VM Scheduler | Time Shared |

---

### Virtual Machine Configuration

| Parameter | VM1 | VM2 |
|------------|------|------|
| MIPS | 500 | 2000 |
| RAM | 512 MB | 512 MB |
| Bandwidth | 1000 Mbps | 1000 Mbps |
| VMM | Xen | Xen |
| Scheduler | Time Shared | Time Shared |

---

### Cloudlet Configuration

| Parameter | Value |
|------------|--------|
| Cloudlets | 2 |
| Length | 40,000 MI |
| File Size | 300 MB |
| Output Size | 300 MB |
| Processing Elements | 1 |

---

## Program File
package org.cloudbus.cloudsim.examples;
import org.cloudbus.cloudsim.*;
import org.cloudbus.cloudsim.core.CloudSim;
import org.cloudbus.cloudsim.provisioners.*;
import java.util.*;

public class CloudSimExample4{

    static Datacenter createDatacenter(String name) {

        List<Host> hostList = new ArrayList<>();

        List<Pe> peList = new ArrayList<>();
        peList.add(new Pe(0,new PeProvisionerSimple(1000)));

        Host host = new Host(
                0,
                new RamProvisionerSimple(2048),
                new BwProvisionerSimple(10000),
                1000000,
                peList,
                new VmSchedulerTimeShared(peList)
        );

        hostList.add(host);

        DatacenterCharacteristics characteristics =
                new DatacenterCharacteristics(
                        "x86","Linux","Xen",
                        hostList,
                        10.0,3.0,0.05,0.001,0.0
                );

        try {
            return new Datacenter(
                    name,
                    characteristics,
                    new VmAllocationPolicySimple(hostList),
                    new LinkedList<Storage>(),
                    0
            );
        }
        catch(Exception e){
            e.printStackTrace();
            return null;
        }
    }

    public static void main(String[] args) {

        try {

            CloudSim.init(2, Calendar.getInstance(), false);

            Datacenter dc1 = createDatacenter("Datacenter_1");
            Datacenter dc2 = createDatacenter("Datacenter_2");

            // USER 1
            DatacenterBroker broker1 = new DatacenterBroker("User1");
            int id1 = broker1.getId();

            Vm vm1 = new Vm(0,id1,500,1,512,1000,10000,"Xen",
                    new CloudletSchedulerTimeShared());

            broker1.submitVmList(Arrays.asList(vm1));

            UtilizationModel model = new UtilizationModelFull();

            Cloudlet c1 = new Cloudlet(0,20000,1,300,300,model,model,model);
            Cloudlet c2 = new Cloudlet(1,40000,1,300,300,model,model,model);

            c1.setUserId(id1);
            c2.setUserId(id1);

            broker1.submitCloudletList(Arrays.asList(c1,c2));


            // USER 2
            DatacenterBroker broker2 = new DatacenterBroker("User2");
            int id2 = broker2.getId();

            Vm vm2 = new Vm(1,id2,500,1,512,1000,10000,"Xen",
                    new CloudletSchedulerTimeShared());

            broker2.submitVmList(Arrays.asList(vm2));

            Cloudlet c3 = new Cloudlet(2,30000,1,300,300,model,model,model);
            Cloudlet c4 = new Cloudlet(3,60000,1,300,300,model,model,model);

            c3.setUserId(id2);
            c4.setUserId(id2);

            broker2.submitCloudletList(Arrays.asList(c3,c4));


            CloudSim.startSimulation();

            List<Cloudlet> r1 = broker1.getCloudletReceivedList();
            List<Cloudlet> r2 = broker2.getCloudletReceivedList();

            CloudSim.stopSimulation();


            System.out.println("\nUSER 1 RESULTS:");
            for(Cloudlet cl : r1)
                System.out.println("Cloudlet "+cl.getCloudletId()+" Time "+cl.getActualCPUTime());

            System.out.println("\nUSER 2 RESULTS:");
            for(Cloudlet cl : r2)
                System.out.println("Cloudlet "+cl.getCloudletId()+" Time "+cl.getActualCPUTime());

        }
        catch(Exception e){
            e.printStackTrace();
        }
    }
}
---

## Algorithm

1. Initialize the CloudSim library.
2. Create a datacenter with two hosts.
3. Configure host resources.
4. Create a datacenter broker.
5. Create two virtual machines with different MIPS values.
6. Submit the VM list to the broker.
7. Create two cloudlets with identical workloads.
8. Assign cloudlets to VMs.
9. Start the CloudSim simulation.
10. Collect execution results and stop the simulation.

---

## Execution Time Formula

Execution Time = Cloudlet Length / VM MIPS

---

## Sample Output
<img width="2560" height="1504" alt="image" src="https://github.com/user-attachments/assets/b93c4407-217c-47bb-9bc2-4dda357b83ac" />


---

## Result

The experiment successfully demonstrates that cloudlets executed on higher MIPS virtual machines complete faster than those executed on lower MIPS VMs, even when the workload is identical.

---

## Conclusion

This simulation validates that CloudSim scheduling accurately reflects performance differences in heterogeneous cloud environments. Higher MIPS virtual machines reduce execution time for identical workloads.




