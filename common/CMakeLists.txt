# Embed some git version information in the binary.
set(GIT_PRE_CONFIGURE_FILE "gitinfo.c.in")
set(GIT_POST_CONFIGURE_FILE "${CMAKE_CURRENT_BINARY_DIR}/gitinfo.c")
include(GitWatcher)

set(COMMON_SRC
    ${GIT_POST_CONFIGURE_FILE}
    lcwcompa.asm
    b64pipe.cpp
    b64straw.cpp
    base64.cpp
    bfiofile.cpp
    blowfish.cpp
    blowpipe.cpp
    blwstraw.cpp
    cdfile.cpp
    ccfile.cpp
    memrev.c
    mixfile.cpp
    sha.cpp
    shapipe.cpp
    shastraw.cpp
    miscasm.asm
    pk.cpp
    pkpipe.cpp
    pkstraw.cpp
    misc.c
    mp.cpp
    int.cpp
    irandoma.asm
    keyfbuffa.asm
    buff.cpp
    irandom.cpp
    drawmisca.asm
    drawmisc.cpp
    drawline.cpp
    drawbuff.cpp
    xordeltaa.asm
    auduncmp.c
    random.cpp
    linear.cpp
    rndstraw.cpp
    fading.c
    framelimit.cpp
    soscodec.c
    winasm.c
    keybuff.cpp
    alloc.cpp
    buffer.cpp
    tobuff.cpp
    tobuffa.asm
    pipe.cpp
    straw.cpp
    face.cpp
    font.cpp
    timer.cpp
    timerdwn.cpp
    palette.cpp
    buffglbl.cpp
    iff.cpp
    lcw.cpp
    lcwpipe.cpp
    lcwstraw.cpp
    load.cpp
    loadpal.cpp
    dipthong.cpp
    wsa.cpp
    _diptabl.cpp
    rawfile.cpp
    getshape.cpp
    windows.cpp
    winhide.cpp
    writepcx.cpp
    delay.cpp
    file.cpp
    stamp.cpp
    packet.cpp
    field.cpp
    rect.cpp
    cliprect.c
    txtprnt.asm
    graphicsviewport.cpp
    xordelta.c
    crc.cpp
    keyframe.cpp
    xpipe.cpp
    xstraw.cpp
)

if (WIN32)
    list(APPEND COMMON_SRC file_win.cpp)
else()
    list(APPEND COMMON_SRC file_posix.cpp)
endif()

set(COMMONR_SRC
    gbuffer.cpp
    interpal.cpp
    wwmouse.cpp
    wwkeyboard.cpp
    soundio_null.cpp
    tcpip_null.cpp
    video_null.cpp
)

set(COMMONV_SRC
    gbuffer.cpp
    interpal.cpp
    wwmouse.cpp
    wwkeyboard.cpp
    tcpip_null.cpp
    unvqbuff.c
    vqaconfig.cpp
    vqadrawer.cpp
    vqaloader.cpp
    vqapalette.cpp
    vqatask.cpp
    vqaver.cpp
)

if(DSOUND)
    list(APPEND COMMONV_SRC soundio.cpp vqaaudio_dsound.cpp)
    list(APPEND VANILLA_LIBS dsound)
else()
    list(APPEND COMMONV_SRC soundio_null.cpp vqaaudio_null.cpp)
endif()

if(DDRAW)
    list(APPEND COMMONV_SRC video_ddraw.cpp)
    list(APPEND VANILLA_LIBS ddraw)
else()
    list(APPEND COMMONV_SRC video_null.cpp)
endif()

if(DSOUND OR DDRAW)
    list(APPEND VANILLA_LIBS dxguid)
endif()

file(GLOB_RECURSE COMMON_HEADERS "*.h")

add_library(common STATIC ${COMMON_SRC} ${COMMON_HEADERS})
target_link_libraries(common PUBLIC ${COMMON_LIBS})
target_include_directories(common PUBLIC .)
# Make build check state of git to check for uncommitted changes.
add_dependencies(common check_git)

if(BUILD_REMASTERTD OR BUILD_REMASTERRA)
    add_library(commonr STATIC ${COMMONR_SRC})
    target_compile_definitions(commonr PUBLIC $<$<CONFIG:Debug>:_DEBUG> ${REMASTER_DEFS})
    target_link_libraries(commonr PUBLIC common ${REMASTER_LIBS})
endif()

# These options currently require directx components from the aug 2007 SDK.
if(BUILD_VANILLATD OR BUILD_VANILLARA)
    add_library(commonv STATIC ${COMMONV_SRC})
    target_compile_definitions(commonv PUBLIC $<$<CONFIG:Debug>:_DEBUG> ${VANILLA_DEFS})
    target_link_libraries(commonv PUBLIC common ${VANILLA_LIBS})
    if(DSOUND)
        target_compile_definitions(commonv PUBLIC DSOUND_BUILD)
    endif()
endif()