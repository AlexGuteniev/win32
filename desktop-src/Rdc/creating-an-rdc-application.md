---
title: Creating an RDC Application
description: This section describes the basic tasks for creating a Remote Differential Compression (RDC) application.
audience: developer
author: REDMOND\\markl
manager: REDMOND\\markl
ms.assetid: 20520a93-7f00-45ae-8b98-df633f0cf188
ms.prod: windows-server-dev
ms.technology: remote-differential-compression
ms.tgt_platform: multiple
keywords:
- creating Remote Differential Compression applications Files
ms.author: windowssdkdev
ms.topic: article
ms.date: 05/31/2018
---

# Creating an RDC Application

This section describes the basic tasks for creating a Remote Differential Compression (RDC) application.

## Implementing the IRdcFileReader Interface

Because the files to be compared might not be actual files on disk, RDC does not perform file I/O directly. Thus, your application must implement the [**IRdcFileReader**](/previous-versions/windows/desktop/api/MsRdc/nn-msrdc-irdcfilereader) interface and its methods for the [**IRdcComparator::Process**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdccomparator-process) method to use for reading signatures sequentially from the seed signature file. Your client application can also use your **IRdcFileReader** methods for copying chunks of file data from the source and seed files.

The [**IRdcFileReader**](/previous-versions/windows/desktop/api/MsRdc/nn-msrdc-irdcfilereader) interface is used by the following:

-   The client application uses the [**IRdcFileReader::Read**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcfilereader-read) method for reading signatures from the source signature file and for reading the needed chunks of file data from the source and seed files.
-   The [**IRdcComparator::Process**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdccomparator-process) method uses the [**Read**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcfilereader-read) method for reading signatures from the seed signature file.
-   The [**IRdcLibrary::CreateComparator**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdclibrary-createcomparator) method uses the [**IRdcFileReader::GetFileSize**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcfilereader-getfilesize) method to obtain the size of the seed signature file.
-   The [**IRdcSignatureReader::ReadHeader**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcsignaturereader-readheader) and [**IRdcSignatureReader::ReadSignatures**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcsignaturereader-readsignatures) methods, which are used for debugging, use the [**Read**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcfilereader-read) method for reading signature and header information from the seed and source signature files.

You are not required to implement the [**IRdcFileReader::GetFilePosition**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcfilereader-getfileposition) method, because it is not used.

## Creating the Signature Generator

Your application must create a signature generator. The client and server use the generator to create signatures for the data in the source and seed files.

**To create a signature generator**

1.  Compute the default recursion depth for the generator by calling the [**IRdcLibrary::ComputeDefaultRecursionDepth**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdclibrary-computedefaultrecursiondepth) method. Note that RDC supports a maximum recursion depth of eight levels. Most applications will need no more than one or two levels.
2.  After you have chosen an appropriate recursion depth for your application, you can create the generator parameters by calling the [**IRdcLibrary::CreateGeneratorParameters**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdclibrary-creategeneratorparameters) method. You will need to call this method once for each level of recursion depth.
3.  Most applications should use the default generator parameter values. If necessary, you can change the default values by calling the [**IRdcGeneratorFilterMaxParameters::SetHashWindowSize**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcgeneratorfiltermaxparameters-sethashwindowsize) and [**IRdcGeneratorFilterMaxParameters::SetHorizonSize**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcgeneratorfiltermaxparameters-sethorizonsize) methods for each of the parameter blocks.
4.  Create the generator by calling the [**IRdcLibrary::CreateGenerator**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdclibrary-creategenerator) method, passing a pointer to the array of parameter blocks in the *iGeneratorParametersArray* parameter.

## Generating the Signatures

After your application has created the signature generator, the server and client can generate signatures by calling the [**IRdcGenerator::Process**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdcgenerator-process) method.

## Creating the Signature Comparators

Your RDC application must create a set of signature comparators, one for each level of recursion depth. (See "Creating the Signature Generator.") Each comparator compares the contents of the seed and source signature files for a given level of recursion and stores the results into a needs list.

To create a comparator, call the [**CreateComparator**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdclibrary-createcomparator) method. Pass an appropriately initialized [**IRdcFileReader**](/previous-versions/windows/desktop/api/MsRdc/nn-msrdc-irdcfilereader) interface pointer for the seed signature file in the *iSeedSignaturesFile* parameter.

## Comparing the Signatures

After your application has created the signature comparators, it can use them to compare the source and seed signatures by calling the [**IRdcComparator::Process**](/previous-versions/windows/desktop/api/MsRdc/nf-msrdc-irdccomparator-process) method. This method produces a needs list, which the client then uses to construct the target file.

## Constructing the Target File

After your application has created the needs list, the client can use that list to construct the target file using chunks of file data from the seed and source files.

The needs list is an array of [**RdcNeed**](/previous-versions/windows/desktop/api/MsRdc/ns-msrdc-__midl___midl_itf_msrdc_0000_0000_0004) structures. The client iterates through this array and processes each **RdcNeed** structure according to the [**RdcNeedType**](/previous-versions/windows/desktop/api/MsRdc/ne-msrdc-__midl___midl_itf_msrdc_0000_0000_0003) value in its **m\_BlockType** member as follows:

-   If the **m\_BlockType** value is **RDCNEED\_SEED**, this means that the chunk is common to the source and seed files. The client copies the chunk locally from the seed file to the target file.
-   If the **m\_BlockType** value is **RDCNEED\_SOURCE**, this means that the client must download the chunk from the server's source file and copy it to the target file.

## Validating the Target File

Your RDC application should verify that the target file created by the client matches the source file on the server. You can do this by computing SHA-1 hash values for the entire source and target files and comparing them. This check can help to detect problems such as signature collisions, cached signature files that might be out of sync with the source or seed files, or other application bugs.

If the target file verification fails, the application must delete the client's target file and copy the source file from the server to the client.

 

 



