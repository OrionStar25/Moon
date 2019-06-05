---
layout: post
title:  "Let's hunt those Memory Leaks!"
date:   2019-06-05
excerpt: "I learn more when I make mistakes than when I succeed"
tag:
- open source
- outreachy
- technology
- gsoc
- research
- python
- jekyll
- fedora
- modularity
feature: https://pubhub.devnetcloud.com/media/sih/site/images/2019-logo/drawable-xxxhdpi/logo.png
comments: false
---

Talk proposal ideas

talk about a personal experience
First off: your talk doesn't need to be exclusive to be useful.
I give talks on "Imposter Syndrome" and "The Sunk-Cost Fallacy" regularly
"Soft skills" talks like that are often forgotten by technical people.
How to get involved with a community where you feel out of your depth: Because (spoiler alert) everyone feels that way at first
How not to panic when your mentor raises the stakes" :)


<orionstar25> @sgallagh can we discuss the current PR now?
<sgallagh> orionstar25: I made some comments on the PR, did you read them>?
<orionstar25> Yes, I did. But the reason why I used `GPtrArray` was because it had the `->len` in it.  
<orionstar25> And a function to get ordered set keys
<orionstar25> Also, can you please explain what `transfer full` means?
* asamalik has quit (Ping timeout: 248 seconds)
<orionstar25> Actually I am not able to understand this entire statement: `You would have wanted (transfer container), which would tell the bindings only to free the array object itself and leave the elements inside alone`
* asamalik (sid314053@gateway/web/irccloud.com/x-vdwydbzehfpqdirt) has joined
* nils has quit (Quit: Leaving)
<sgallagh> orionstar25: Sorry, I'm in meetings for the next 30 minutes. I'll talk with you then
<sgallagh> orionstar25: I'm back now. Sorry for the delay
<sgallagh> orionstar25: Take a look at the allocation function for GPtrArray: https://developer.gnome.org/glib/stable/glib-Pointer-Arrays.html#g-ptr-array-new-with-free-func
<sgallagh> (or `g_ptr_array_new_full()`)
<sgallagh> The array lets you set a function to be called on each element when the array itself is freed.
<sgallagh> Which means you don't need to individually free the elements manually, you just call `g_ptr_array_unref()` and it will also clean up the array contents.
* jplesnik has quit (Quit: Leaving)
<sgallagh> `(transfer full)` on a container type (GPtrArray, GArray and GHashTable) tells the binding "You are responsible for freeing the array container and all of its members".
<sgallagh> `(transfer container)` on a container type tells it "You only need to free the container itself. The elements are handled elsewhere."
* bowlofeggs (~bowlofegg@fedora/bowlofeggs) has joined
<orionstar25> That makes sense! Got it. I've changed the type to GStrv now. Still can't seem to figure out the hashtable declaration issue.
<sgallagh> orionstar25: I just gave you another hint on the PR.
<orionstar25> But I have set GHashtable to NULL initially :3 I'll look into it
* dmach has quit ()
<sgallagh> orionstar25: Right, there are two mistakes there :)
<sgallagh> orionstar25: Mistake 1: you never call `g_hash_table_new_full()`
<sgallagh> Mistake 2: You never free the hash table.
<sgallagh> You can fix mistake 2 by using `g_autoptr(GHashTable) stream_name = NULL;`
<sgallagh> Which will cause the compiler to automatically call `g_hash_table_unref()` on it when it goes out of scope.
* fivaldi_ has quit (Read error: Connection reset by peer)
* fivaldi_ (~fivaldi@ip-185-93-63-210.v4.cust.lemointernet.cz) has joined
* crobinso has quit (Ping timeout: 252 seconds)
* giulia has quit (Quit: Leaving)
<orionstar25> :O 
* mjwolf has quit (Read error: Connection reset by peer)
* lholecek has quit (Quit: Konversation terminated!)
* halfline has quit (Quit: ZNC - http://znc.in)
* bgoncalv has quit (Quit: Leaving.)
* halfline (~rstrode@144.121.20.162) has joined
* Defolos (~Defolos@ip-184-209-178-54.spfdma.spcsdns.net) has joined
* mjwolf (~mjwolf@96-42-239-82.dhcp.roch.mn.charter.com) has joined
<orionstar25> @sgallagh will you give me hints on the other error? :( 
* mjwolf has quit (Read error: Connection reset by peer)
<sgallagh> orionstar25: I gave you the answer already
<sgallagh> Read carefully please
<orionstar25> No thats fixed. the other one :3
<sgallagh> Which one are you talking about?
<sgallagh> I gave you both answers
<orionstar25> `libmodulemd-CRITICAL **: 20:44:07.594: modulemd_module_set_module_name: assertion 'module_name' failed
<orionstar25> `
<orionstar25> This one still persists. Ill push my code.
<sgallagh> orionstar25: is that causing your test to fail?
<sgallagh> It may be coming from another test. The logs sometimes show expected failures
<sgallagh> Such as tests to make sure we gracefully handle bad input
<orionstar25> That is causing my tests to fail. Its working when there are streams in a module. Its failing when I add streams
<sgallagh> Are you adding streams without a module name?
<sgallagh> Because that’s what the error says
<orionstar25> m = modulemd_module_new ("testmodule");
<sgallagh> That’s not a stream
<orionstar25> stream = modulemd_module_stream_new (2, "testmodule", "stream1");
<sgallagh> Ok
<orionstar25> modulemd_module_add_stream (m, stream, MD_MODULESTREAM_VERSION_UNSET, NULL);
<sgallagh> I have to run to an appointment. Try running it in gdb or with valgrind and see if you find any problems
<orionstar25> Okay
* mjwolf (~mjwolf@96-42-239-82.dhcp.roch.mn.charter.com) has joined
* rh-jkang (~jkang@2607:fea8:3d60:dc9::d) has joined
* lruzicka has quit (Remote host closed the connection)
* paragan has quit (Remote host closed the connection)
* jalalsfs has quit (Ping timeout: 248 seconds)
* Defolos has quit (Read error: Connection reset by peer)
* jalalsfs (~jalalsfs@unaffiliated/jalalsfs) has joined
* vmaljulin has quit (Quit: Leaving)
* crobinso (~crobinso@redhat/crobinso) has joined
* Defolos (~Defolos@ip-184-209-141-132.spfdma.spcsdns.net) has joined
* jalalsfs has quit (Ping timeout: 272 seconds)
* jalalsfs (~jalalsfs@unaffiliated/jalalsfs) has joined
<orionstar25> @sgallagh, I used gdb back trace, and a bunch of other trace commands. I used valgrind leak check and got the same error. I think I'm stuck :/
<sgallagh> orionstar25: OK, give me a moment to pull your branch and we'll walk through this together
<orionstar25> yayy 
* fivaldi_ has quit (Ping timeout: 246 seconds)
<sgallagh> orionstar25: We'll start by running just your new test, so we don't have to debug any of the other tests
<orionstar25> hmm 
<sgallagh> meson test --print-errorlogs module_v2_debug --test-args "-p /modulemd/v2/module/stream_names"
<sgallagh> `meson test --print-errorlogs module_v2_debug --test-args "-p /modulemd/v2/module/stream_names"`
<sgallagh> You will notice that you see no mention of the assertion failure.
<sgallagh> That's because that error comes from a different test
<orionstar25> Yes :/
<orionstar25> :'(
<sgallagh> What do you see instead?
<orionstar25> I see no failures
<sgallagh> uh... you should
<sgallagh> Can you pastebin the testlog.txt?
<orionstar25> yes
<orionstar25> https://paste.fedoraproject.org/paste/TaxI1VSDQke4dgb9JXpScQ
<sgallagh> 0.01 seconds suggests that you didn't actually run any tests
<sgallagh> Can you please show me the exact command you ran?
<sgallagh> Also, that's not testlog.txt. I wanted to see `/home/orionstar25/libmodulemd/api2/meson-logs/testlog.txt`
<sgallagh> Which will include the arguments it was called with
<sgallagh> My guess is that you have a typo in the `-p` value and it's not matching an actual test.
<sgallagh> Which means it runs zero tests and exists
<sgallagh> *exits
<orionstar25> I ran the command you gave above'
* pradeepgangwar (6ac6a449@gateway/web/cgi-irc/kiwiirc.com/ip.106.198.164.73) has joined
<orionstar25> before I ran meson test module_v2_debug
<sgallagh> orionstar25: Copied and pasted?
<orionstar25> Yes
<sgallagh> Please pastebin testlog.txt like I asked
<sgallagh> It will make it easier for me to see
<orionstar25> yes just one min
* pradeepgangwar (6ac6a449@gateway/web/cgi-irc/kiwiirc.com/ip.106.198.164.73) has left
<sgallagh> ok
<sgallagh> (note that you can call `fpaste /home/orionstar25/libmodulemd/api2/meson-logs/testlog.txt` and it will upload it for you
<orionstar25> https://paste.fedoraproject.org/paste/jSBRUkr15P-VZOV4gPPs7A
<sgallagh> ok, that's odd.
<sgallagh> On my system, it's failing with "free(): double free detected in tcache 2"
<sgallagh> Which should be detectable by valgrind, so let's see if it picks that up on your system as well.
<orionstar25> Okay
<sgallagh> (BTW, are you running on x86_64, i686 or something else?)
<orionstar25> 64 bit
<sgallagh> `meson test --wrap="valgrind --leak-check=full" --print-errorlogs module_v2_debug --test-args "-p /modulemd/v2/module/stream_names"`
<sgallagh> orionstar25: ^^ run that and then look at the testlog-valgrind.txt
<orionstar25> https://paste.fedoraproject.org/paste/n3YGKJV44Tsc9wq4ZZctiQ
<sgallagh> orionstar25: What does that output show you?
<orionstar25> That there were no memory leaks
<sgallagh> orionstar25: No *leaks*, but there are memory *errors*
<sgallagh> Look near the top
<orionstar25> though I didn't undertsnad that somewhere there is ??? in some places
<sgallagh> orionstar25: That means that it is code that is coming from another library and you don't have the debuginfo installed for that code
<sgallagh> You can safely ignore that most of the time, unless you end up discovering a bug in the other library.
* fivaldi_ (~fivaldi@ip-185-93-63-210.v4.cust.lemointernet.cz) has joined
<sgallagh> But if you want to have real functions there, do `debuginfo-install glib2 gobject-introspection`
<sgallagh> (as root or with `sudo`)
<orionstar25> I found the 'memory error' part
<orionstar25> invalid free() detected.
<sgallagh> Right, what else does that block tell you?
<orionstar25> It was able to cleanup the hashtables automatically
<sgallagh> orionstar25: that error note has three distinct sections
<sgallagh> 1) The location the error was detected.
<orionstar25> Okay, just a minute
<sgallagh> 2) The location where the memory being accessed was previously freed
<sgallagh> 3) The location where it was originally allocated.
<sgallagh> From those three things, you should be able to follow your (short) code and discover where it goes wrong.
<sgallagh> Pay close attention to anywhere in your code that you may call functions that end in `_free()`, `_ref()` or `_unref()`
<sgallagh> Because those are the most likely places for you to make a mistake with memory handling
<sgallagh> You need to have the same number of free()/unref() calls as you do allocations/ref()s
* Defolos has quit (Ping timeout: 248 seconds)
<orionstar25> So memory errors don't account for failed valgrind tests?
<sgallagh> orionstar25: I'm not sure I understood you
<sgallagh> `valgrind` doesn't return an error unless valgrind itself fails, so when you `--wrap=valgrind`, the test will still return "success"
<sgallagh> You just need to examine the output manually.
<sgallagh> There's special code I wrote in the `meson test valgrind` that causes it to report a failure if it detects a leak or error.
<sgallagh> It's a real hack though :)
<sgallagh> orionstar25: I need to go pick up my daughter at the school bus stop. Back in fifteen minutes or so. See if you can spot where you free the same memory twice while I do that.
<orionstar25> Alright
* fivaldi_ has quit (Ping timeout: 258 seconds)
* jkrieger (~jkrieger@212.53.230.89) has joined
* jkrieger has quit (Client Quit)
<orionstar25> I found it. :3 And I also noticed the difference in the memory detector region in the 2 cases. Up unti now I only used to look at the test summary.
<sgallagh> orionstar25: Explain it to me, please
<sgallagh> (I'm trying to help you build the skills to track these things down, so explaining your process helps you internalize it too)
<orionstar25> Okay, so what I did now to correct it is that in the function `g_hash_table_new_full()` I assigned NULL to both the ways of freeing the keys and values.
<orionstar25> Since we had no values, I had assigned that NULL already
<orionstar25> Thinking that keys will need a way to get freed. 
<sgallagh> Can you explain to me why you don't need to free them?
<orionstar25> But (I think this is the reason, not sure), because we're using _autoptr, it will free it on its own (as seen by the valgrind log too)
<sgallagh> (I'm not saying your solution is wrong, I'm asking you to justify it)
<sgallagh> So, you've got the right solution, but the wrong reason for it.
<orionstar25> So, providing a way to free the key values will be doubly freeing it
<orionstar25> Oh okay
<sgallagh> So let's walk through it.
<orionstar25> Hmm
<sgallagh> starting at `g_hash_table_new_full()`, you allocate the memory for the GHashTable container.
<sgallagh> You allocated it with a `g_free()` function for the keys.
<sgallagh> Which means that when the GHashTable is freed (such as when it goes out of scope and is freed by the autoptr), it will call that function on all of the keys.
<sgallagh> That makes sense, and it's exactly what you want *if* the hash table owns the memory of its keys.
<sgallagh> However, when you added the keys with `g_hash_table_add()`, you added the return value of `modulemd_module_stream_get_stream_name()` to it directly.
<sgallagh> So let's look at the definition of that function:  https://fedora-modularity.github.io/libmodulemd/latest/modulemd-2.0-ModulemdModuleStream.html#modulemd-module-stream-get-stream-name
<sgallagh> orionstar25: What is the `transfer` value for the return value of this function?
<orionstar25> doesnt transfer ownership
<orionstar25> only the value
<sgallagh> right, so that means that the caller is not expected to `free()` it.
<sgallagh> Sorry, rephrase that from "not expected to" to "must not"
<orionstar25> super interesting :O
<sgallagh> So there are two ways that you can resolve this problem: you can `g_strdup()` the string into the GHashTable, making a copy of it that you can free... or you can drop the free function from the hash initialization.
<sgallagh> So, now we need to know which of those  is the better approach.
<sgallagh> Copying the memory and then allowing the GHashTable to free it is safer if there's a chance that the memory it's pointing to could be changed before you need it for something.
<sgallagh> But it comes at the performance cost of having to do a memory allocation.
<sgallagh> So we should see if we can avoid that.
<sgallagh> orionstar25: So the next question: where do we use the stream names in this function?
<orionstar25> To add to the GHashtable (?)
<orionstar25> as keys
<sgallagh> After that. Where do we consume those keys?
<orionstar25> You said " it is safer if there's a chance that the memory it's pointing to could be changed before you need it for something"
<orionstar25> butthe return value is a const, so we can safely assume that the values wont be changes, yes?
<orionstar25> changed*
<sgallagh> No, not at all
<orionstar25> To create an ordered set
<sgallagh> const means that the consumer can't change it. But the object that owns it has no such restriction.
<sgallagh> So if you think it might get changed before you can use it, a copy is required.
<sgallagh> Right, so let's look at the implementation of `modulemd_ordered_str_keys_as_strv()`
<orionstar25> its doing `g_ptr_array_free (keys, FALSE);` so that its not freeing twice :O
<sgallagh> Sorry, had to answer a phone call
<sgallagh> I'm back
<sgallagh> orionstar25: That's part of it
* asamalik_ (sid314053@gateway/web/irccloud.com/x-wkoluqkgetfoggih) has joined
<sgallagh> But notice that in `modulemd_ordered_str_keys()`, it explicitly makes a `g_strdup()` of the strings when adding them to the array.
<sgallagh> So we now know that the data coming back from `modulemd_ordered_str_keys()` is  not pointing at the original GHashTable internal values, but has its own memory.
<orionstar25> hmm
<sgallagh> (side-note: this really needs to be added to gtkdoc so we don't have to look at the implementation, and I've asked merlinm to look into doing that)
<sgallagh> orionstar25: Which means that `modulemd_module_get_stream_names_as_strv()` can safely return it as-is, because it's not coupled to the stream_names GHashTable *or* the underlying ModuleStream object.
<sgallagh> It's fresh and can be passed back as `(transfer full)` safely.
* crobinso has quit (*.net *.split)
* asamalik has quit (*.net *.split)
* rh-jkang has quit (*.net *.split)
* asamalik_ is now known as asamalik
<sgallagh> orionstar25: So, since that function is returning memory that is fully self-contained, it's safe to return it. Since nothing will change it underneath us and the API consumer can hold onto it however long they need to.
<orionstar25> So if I use g_strdup() in my function, even `modulemd_ordered_str_keys()` doesnt need to make a copy
<sgallagh> It wouldn't *need* to, but since it already is, we don't need to do it again.
<sgallagh> And `modulemd_ordered_str_keys()` is the right layer to do that.
<orionstar25> right,so in that case, transfer none option works fine (?)
<sgallagh> It takes arbitrary input, stabilizes it and gives it back.
<sgallagh> orionstar25: Sorry, please rephrase the question with more detail
* crobinso (~crobinso@redhat/crobinso) has joined
* fivaldi_ (~fivaldi@ip-185-93-63-210.v4.cust.lemointernet.cz) has joined
<sgallagh> orionstar25: I'm trying to make sure that you're following along and not getting lost in the explanation.
<sgallagh> Memory management is subtle and quick to anger :)
<orionstar25> I meant, that since we already have a generalized layer that makes a copy and returns a fully self-contained value, we wouldn't need to make a copy in our get_stream_names function.  
<sgallagh> Correct
<sgallagh> And so making the copy there would be harmless but wasteful.
<sgallagh> (Assuming we also cleaned it up properly)
<orionstar25> So the 1st solution is good enough (assigning NULL for freeing values)
<sgallagh> Correct
<orionstar25> yayy
<sgallagh> Not just "good enough" but, as we have just explored, it's the best answer
<sgallagh> But I wanted to walk you through *why* it was the best answer, so you might be able to do the same yourself next time.
<orionstar25> Yes, thank you, I needed that desperately :D
<sgallagh> orionstar25: As a general policy, if you're ever *unsure* if the memory is going to change on you, make the copy. But in this case, we can prove that it won't, so we don't need to.
 FiSHLiM plugin unloaded
 Python interface unloaded
* Now talking on #fedora-modularity
* Topic for #fedora-modularity is: Fedora Modularity -- https://docs.pagure.org/modularity/ | Temporarily for registered users only to fight the spambots
* Topic for #fedora-modularity set by contyk!~contyk@redhat/contyk (Wed Aug  1 21:40:05 2018)
* Channel #fedora-modularity url: https://docs.pagure.org/modularity/
<orionstar25> @sgallagh I could just copy paste yesterday's entire conversation and my week 3 blog is done hahaha
<sgallagh> orionstar25: That would be plagiarism!
<sgallagh> But it probably is a good source of material.
* merlinm (~mmathesi@c-24-12-48-66.hsd1.in.comcast.net) has joined
<orionstar25> I'd say its a different format than anyone else writing their blog :D
<dmach> dalley, I don't see any problem; the provides are captured in the repodata exactly according to what you can find in the rpm headers
* Disconnected ()
* orionstar25 sets mode +Z on orionstar25
* orionstar25 sets mode +i on orionstar25
-NickServ- This nickname is registered. Please choose a different nickname, or identify via /msg NickServ identify <password>.
-NickServ- You are now identified for orionstar25.
* Now talking on #fedora-modularity
* Topic for #fedora-modularity is: Fedora Modularity -- https://docs.pagure.org/modularity/ | Temporarily for registered users only to fight the spambots
* Topic for #fedora-modularity set by contyk!~contyk@redhat/contyk (Wed Aug  1 21:40:05 2018)
* Channel #fedora-modularity url: https://docs.pagure.org/modularity/
<orionstar25> @sgallagh, I'll continue to code the YAML -> .pot ?
* Disconnected ()
 No channel joined. Try /join #<channel>
 Not connected. Try /server <host> [<port>]
 Not connected. Try /server <host> [<port>]
 FiSHLiM plugin unloaded
 Python interface unloaded
* Now talking on #fedora-modularity
* Topic for #fedora-modularity is: Fedora Modularity -- https://docs.pagure.org/modularity/ | Temporarily for registered users only to fight the spambots
* Topic for #fedora-modularity set by contyk!~contyk@redhat/contyk (Wed Aug  1 21:40:05 2018)
* Channel #fedora-modularity url: https://docs.pagure.org/modularity/
* Disconnected ()
 Not connected. Try /server <host> [<port>]
 FiSHLiM plugin unloaded
 Python interface unloaded
* Now talking on #fedora-modularity
* Topic for #fedora-modularity is: Fedora Modularity -- https://docs.pagure.org/modularity/ | Temporarily for registered users only to fight the spambots
* Topic for #fedora-modularity set by contyk!~contyk@redhat/contyk (Wed Aug  1 21:40:05 2018)
* Channel #fedora-modularity url: https://docs.pagure.org/modularity/
* langdon_ has quit (Ping timeout: 246 seconds)
* langdon (~langdon@209.6.104.192) has joined
* jcajka (~jcajka@ip-78-45-65-107.net.upcbroadband.cz) has joined
* geppetto (~james@32.208.163.99) has joined
* langdon has quit (Ping timeout: 245 seconds)
* langdon (~langdon@209.6.104.192) has joined
* Now talking on #fedora-modularity
* Topic for #fedora-modularity is: Fedora Modularity -- https://docs.pagure.org/modularity/ | Temporarily for registered users only to fight the spambots
* Topic for #fedora-modularity set by contyk!~contyk@redhat/contyk (Wed Aug  1 21:40:05 2018)
* Channel #fedora-modularity url: https://docs.pagure.org/modularity/
* mjwolf (~mjwolf@2620:1f7:890:43f0::1:ae) has joined
<otaylor> asamalik: can you add kalev to https://pagure.io/fedora-docs/flatpak ?
* bgoncalv has quit (Quit: Leaving.)
<asamalik> otaylor: sure
<asamalik> otaylor: do you want to be an admin in that repo?
<otaylor> asamalik: Sure
<asamalik> otaylor: ah! you are an admin
<asamalik> so you should be able to add users
<otaylor> mm, I didn't see that
<otaylor> let me look again :-)
* otaylor looks again and he is an admin ... not sure how I missed that
* fivaldi_ (~fivaldi@ip-185-93-63-210.v4.cust.lemointernet.cz) has joined
<otaylor> asamalik: Sorry for trouble
<asamalik> otaylor: ah no worries at all!
<asamalik> glad it worked
<kalev> thanks guys
* mschorm (~mschorm@ip-86-49-125-189.net.upcbroadband.cz) has joined
* paragan has quit (Ping timeout: 272 seconds)
* dmach has quit ()
* paragan (~paragan@unaffiliated/paragan) has joined
* lholecek has quit (Quit: Konversation terminated!)
* cstratak_ has quit (Ping timeout: 258 seconds)
* ttereshc has quit (Quit: Leaving)
* Defolos has quit (Ping timeout: 248 seconds)
* nils has quit (Quit: Leaving)
* rh-jkang has quit (Quit: Leaving)
* fivaldi_ has quit (Ping timeout: 272 seconds)
* hhorak (~hhorak@95.82.135.131) has joined
* ttereshc (~ttereshc@85.163.10.190) has joined
* giulia has quit (Quit: Leaving)
* hhorak has quit (Quit: Leaving.)
* pradeepgangwar (67cedffa@gateway/web/cgi-irc/kiwiirc.com/ip.103.206.223.250) has joined
* paragan has quit (Remote host closed the connection)
<orionstar25> @sgallagh I used `get_stream_names_as_strv()` . Does the `as_strv` part get removed automatically in the python bindings? I don't see anything in the bindings folder
-SaslServ- orionstar2520!7ab2c622@122.178.198.34 has just authenticated as you (orionstar25)
* orionstar2520 (7ab2c622@122.178.198.34) has joined
 FiSHLiM plugin unloaded
 Python interface unloaded
* Now talking on #fedora-modularity
* Topic for #fedora-modularity is: Fedora Modularity -- https://docs.pagure.org/modularity/ | Temporarily for registered users only to fight the spambots
* Topic for #fedora-modularity set by contyk!~contyk@redhat/contyk (Wed Aug  1 21:40:05 2018)
* Channel #fedora-modularity url: https://docs.pagure.org/modularity/
* pradeepgangwar (67cedffa@gateway/web/cgi-irc/kiwiirc.com/ip.103.206.223.250) has left
<sgallagh> orionstar25: Yes, that’s what the `(rename-to)` directive does.
* pbrobinson (~pbrobinso@dozer.roving-it.com) has left ("Leaving")
<orionstar25> Alright, I'll just make a PR to accommodate that.
<orionstar25> I thought so too, but then in the file which you told me to make the change in, there were C functions. I expected python function declarations
<orionstar25> I'm assuming this is a G object toolkit functionality (?)
<sgallagh> orionstar25: Right, we always write the implementation in C
<sgallagh> then the GObject Introspection generates bindings from the C and the GTKdoc we have
<sgallagh> Regarding https://github.com/fedora-modularity/libmodulemd/pull/307... how are you testing it?
<sgallagh> I'm guessing that you're just using the system copy of libmodulemd, which wouldn't have your new function yet.
<orionstar25> I rebased
<sgallagh> How are you performing the test?
<orionstar25> `git pull upstream master --rebase`
<orionstar25> Oh, not test exactly. Just checking the length of my catalog.
<sgallagh> That doesn't change what is installed to the operating system though.
<sgallagh> so just doing `from gi.repository import Modulemd` won't give you those changes.
<orionstar25> Oooohokay! I'll update it here too
<sgallagh> You need to be testing against the version in your checkup
<sgallagh> err, checkout
<sgallagh> orionstar25: If you look at meson.build: https://github.com/fedora-modularity/libmodulemd/blob/master/modulemd/v2/meson.build#L106
<sgallagh> You'll see there are some environment variables that you need to set in order to tell Python to look for the bindings in your build directory instead of the default system paths
<sgallagh> GI_TYPELIB_PATH and LD_LIBRARY_PATH both have to point to the build directory (and specifically, the`modulemd/v2` path under that directory)
* Defolos (~Defolos@dslb-094-222-075-044.094.222.pools.vodafone-ip.de) has joined
<sgallagh> GI_TYPELIB_PATH tells it where to find the  Modulemd.typelib file and LD_LIBRARY_PATH tells it where to find the C library
<sgallagh> orionstar25: If you have those two environment variables set (and have run `ninja` to build the necessary pieces), then your test will see your new changes.
<orionstar25> Got it, i'll just try this.
<sgallagh> orionstar25: Also, please run `ninja test` before pushing commits to your PRs.
<sgallagh> The CI checks whether you ran the autoformatter and fails if it detects that it needed to make changes.
* mschorm has quit (Ping timeout: 268 seconds)
* kalev has quit (Ping timeout: 248 seconds)
* fivaldi_ (~fivaldi@ip-185-93-63-210.v4.cust.lemointernet.cz) has joined
* Defolos has quit (Ping timeout: 252 seconds)
* fivaldi_ has quit (Ping timeout: 245 seconds)
* fivaldi_ (~fivaldi@ip-185-93-63-210.v4.cust.lemointernet.cz) has joined
* fivaldi_ has quit (Ping timeout: 248 seconds)
* kalev (~kalev@fedora/kalev) has joined
<orionstar25> @sgallagh, I set those 2 paths. They are `/home/orionstar25/libmodulemd/modulemd/v2`. Also being shown in `os.environ`. Its not working still. Same error.
<sgallagh> orionstar25: no, those are the wrong paths
<sgallagh> It has to be the build dir, not the source dir
<sgallagh> If you're using "api2" as the build dir (which I think I saw you were in yesterday's pastes) then you want `/home/orionstar25/libmodulemd/api2/modulemd/v2`
<sgallagh> If you look in that folder, it should have a file named Modulemd-2.0.gir and also libmodulemd.so.2
<orionstar25> Oh okay, thats why it says meson.root_dir() + libmodulemd/v2
<sgallagh> yes
<sgallagh> orionstar25: Better?
<orionstar25> No :3
* mjwolf has quit (Quit: Leaving)
<sgallagh> Can you be more specific?
<orionstar25> I'm getting the same error
<orionstar25> `'Module' object has no attribute 'get_stream_names'`
<sgallagh> orionstar25: Can you show me exactly what you're doing?
<orionstar25> ive compiled using ninja. Then I run Utils.py in ModulemdTranslationHelpers directory.
<sgallagh> orionstar25: Exactly how do you run it?
<orionstar25> python3 Utils.py
<orionstar25> https://paste.fedoraproject.org/paste/GWuPYto5kMRergjMjk59Pg
<orionstar25> Oh wait. Give me a moment please
<sgallagh> So you run it without setting the environment variables I mentioned.
<orionstar25> No I set them. That is working. The error is something else now. I'll ttake 5min
* mschorm (~mschorm@2a00:1028:83a2:e5e:f3f8:a035:ec72:d30f) has joined
<orionstar25> Okay, now its not able to import anything. 
* otaylor has quit (Ping timeout: 272 seconds)
<orionstar25> https://paste.fedoraproject.org/paste/Hpeslg-C9TwjAp8jcUI-~Q
<orionstar25> Not even `gi`
<orionstar25> Alright, thats fixed! 
<sgallagh> What did you do?
<orionstar25> I appended $HOME to my imports
<sgallagh> Uh, what?
<orionstar25> Now the new error is that `search_streams` is not an attribute of `module`. It doesnt have a rename-to for itself. 
<orionstar25> Oh okay, so, my path got changed. So none of my imports were being found
<orionstar25> So I did `sys.path.append('/home/orionstar25/')`
<sgallagh> Why did your path change?
<orionstar25> because I set the environment variables in `.bashrc`
<sgallagh> Don’t do that.
<sgallagh> You can just set them on the command line when you call python3
<sgallagh> `GI_TYPELIB_PATH=/home/orionstar25/libmodulemd/... python3 yourtest.py`
<sgallagh> You don’t want to break your operating environment.
<orionstar25> Okay
<orionstar25> Yes, that works. I'll add the rename to for search-stream
<orionstar25> Sorry, I just need to add it in binding renames if I'm not renaming any function?
<sgallagh> Yes