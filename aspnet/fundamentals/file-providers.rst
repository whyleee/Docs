.. include:: /../common/stub-topic.txt

|stub-icon| File Providers
==========================

.. include:: /../common/stub-notice.txt

.. _issue: https://github.com/aspnet/Docs/issues/206

File Provider is abstraction for accessing file system in ASP.NET, it provides file acess, directory listings and file watcher abilities.

.. contents:: Sections
  :local:
  :depth: 1

IHostingEnvironment File Providers
----------------------------------

:dn:iface:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment` contains two main file system providers:

    :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider` - by default is :dn:class:`~Microsoft.Extensions.FileProviders.PhysicalFileProvider` constructed using :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath`. It's used for loading apllication resources like ASP.NET MVC Views that are not intended to be served directly.

    :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath` is set to current directory in default template by running :dn:meth:`~Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseContentRoot`. When starting application using `dotnet run` it would be set to you project directory, when running published application via ``dotnet app.dll`` you must make sure that current directory is set to application publish location.

    .. code-block:: c#
        :emphasize-lines: 3

        var host = new WebHostBuilder()
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory())
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();


    :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.WebRootFileProvider` defaults :dn:class:`~Microsoft.Extensions.FileProviders.PhysicalFileProvider` constructed from :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.WebRootPath` or if it's empty ``wwwroot`` sub-directory under :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath` and is used by StaticFiles middlware with default settings or other middlware that server externaly accessible content.
    If ``wwwroot`` directory does not exist and :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.WebRootPath` is not explictly set to other location :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.WebRootFileProvider` is set to :dn:class:`~Microsoft.Extensions.FileProviders.NullFileProvider`

    :dn:prop:`~Microsoft.AspNetCore.Hosting.IHostingEnvironment.WebRootPath` could be changed by running :dn:meth:`~Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseWebRoot` extension method on :dn:class:`~Microsoft.AspNetCore.Hosting.WebHostBuilder`.

    .. code-block:: c#
        :emphasize-lines: 6

        var host = new WebHostBuilder()
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory())
            .UseIISIntegration()
            .UseStartup<Startup>()
            .UseWebRoot("public")
            .Build();


PhysicalFileProvider
--------------------
:dn:class:`~Microsoft.Extensions.FileProviders.PhysicalFileProvider` is commonly used implementation backed by physical file system. It folows same casing conventions as underlying file system (i.e. case-sensetive on Linux, case-insensetive on Windows) and forbids usage of absolute paths in file paths.

EmbeddedFileProvider
--------------------
:dn:class:`~Microsoft.Extensions.FileProviders.EmbeddedFileProvider` uses assembly embedded files as a file system. This file provider is case sensetive and uses a flat directory structure. Everything under the base namespace is considered to be one directory.

CompositeFileProvider
---------------------
:dn:class:`~Microsoft.Extensions.FileProviders.CompositeFileProvider` alows combining multiple providers into one, if there are multiple files/directories with the same name in different providers first one is preffered in order which they we passed to :dn:class:`~Microsoft.Extensions.FileProviders.CompositeFileProvider` constructor.

NullFileProvider
----------------
:dn:class:`~Microsoft.Extensions.FileProviders.NullFileProvider` is file provider that returns not found for all requests.
