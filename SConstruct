import SCons

env = Environment()
env.CacheDir('cache')
env.Decider('MD5-timestamp')
env.SetOption('max_drift', 1)

def monkey_get_csig(self):
    """
    Generate a node's content signature, the digested signature
    of its content.

    node - the node
    cache - alternate node to use for the signature cache
    returns - the content signature
    """
    ninfo = self.get_ninfo()
    try:
        return ninfo.csig
    except AttributeError:
        pass

    csig = self.get_max_drift_csig()
    if csig is None:

        try:
            if self.get_size() < SCons.Node.FS.File.md5_chunksize:
                contents = self.get_contents()
            else:
                csig = self.get_content_hash()
        except IOError:
            # This can happen if there's actually a directory on-disk,
            # which can be the case if they've disabled disk checks,
            # or if an action with a File target actually happens to
            # create a same-named directory by mistake.
            csig = ''
        else:
            if not csig:
                csig = SCons.Util.MD5signature(contents)

        print "Computed csig for", str(self), csig

    ninfo.csig = csig

    return csig

SCons.Node.FS.File.get_csig = monkey_get_csig

env.SConscript(
    dirs=['.'],
    variant_dir='variant',
    duplicate=False,
    exports=['env'],
)

